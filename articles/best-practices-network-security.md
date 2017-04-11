<properties
   pageTitle="Segurança de rede e serviços de nuvem do Microsoft | Microsoft Azure"
   description="Aprenda alguns dos principais recursos disponíveis no Azure para ajudar a criar ambientes de rede seguros"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Segurança de rede e serviços de nuvem do Microsoft

Serviços de nuvem da Microsoft oferecem serviços de hyperscale e infraestrutura, recursos de nível empresarial e muitas opções para conectividade de híbrido. Os clientes podem escolher acessar esses serviços através da Internet ou com rota expressa do Azure, que fornece conectividade de rede privada. A plataforma do Microsoft Azure permite aos clientes estender sua infraestrutura na nuvem diretamente e criar arquiteturas multicamadas. Além disso, terceiros pode habilitar recursos aprimorados, oferecendo serviços de segurança e dispositivos virtuais. Este white paper fornece uma visão geral dos problemas de segurança e arquitetônicos que clientes devem considerar ao usar os serviços de nuvem da Microsoft acessados através do rota expressa. Ela também aborda criando serviços mais seguros em redes virtuais Azure.

## <a name="fast-start"></a>Início rápido
O gráfico a seguir lógica pode direcionar a um exemplo específico de várias técnicas de segurança disponíveis com a plataforma Windows Azure. Para referência rápida, encontre o exemplo que melhor se ajuste a seu caso. Para obter explicações mais completas, continue a leitura por meio de papel.
![Fluxograma de opções de segurança][0]

[Exemplo 1: Crie uma rede de perímetro (também conhecido como DMZ, zona desmilitarizada e sub-rede filtrada) para ajudar a proteger aplicativos com grupos de segurança de rede (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Exemplo 2: Crie uma rede de perímetro para ajudar a proteger aplicativos com um firewall e NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemplo 3: Crie uma rede de perímetro para ajudar a proteger as redes com um firewall, rota definidos pelo usuário (UDR) e NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Exemplo 4: Adicione uma conexão de híbrido com uma dispositivo virtual-to-site rede virtual privada (VPN).](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemplo 5: Adicione uma conexão de híbrido com um gateway-to-site, Azure VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Exemplo 6: Adicione uma conexão de híbrido com rota expressa.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Exemplos de adição de conexões entre redes virtuais, alta disponibilidade e serviço encadeamento serão adicionados a este documento sobre os próximos meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Proteção de conformidade e infraestrutura da Microsoft
Microsoft deu uma posição de liderança suporte a iniciativas de conformidade necessárias para clientes corporativos. A seguir estão alguns das certificações de conformidade para Azure: ![identificações de conformidade do Azure][1]

Para obter mais detalhes, consulte as informações de conformidade no [Microsoft Central de confiabilidade](https://azure.microsoft.com/support/trust-center/compliance/).

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura de nuvem necessária para executar hyperscale serviços globais. Infraestrutura de nuvem da Microsoft inclui hardware, software, redes e administrativos e a equipe de operações, além dos data centers físicas.

![Recursos de segurança do Azure][2]

Essa abordagem fornece uma base mais segura para os clientes implantar os serviços de nuvem da Microsoft. A próxima etapa é para clientes projetar e criar uma arquitetura de segurança para proteger esses serviços.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquiteturas de segurança tradicionais e redes de perímetro
Embora a Microsoft investe pesado em Protegendo a infraestrutura de nuvem, os clientes também devem proteger seus serviços de nuvem e grupos de recursos. Uma abordagem de várias camada de segurança fornece a melhor defesa. Uma zona de segurança de rede de perímetro protege recursos internos de rede de uma rede não confiável. Uma rede de perímetro refere-se à bordas ou partes da rede estabelecidos entre a Internet e a infraestrutura de empresa protegida.

Em redes corporativo típico, a infraestrutura de núcleo muito proteção reforçada no perímetro, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e pontos de imposição de política. Dispositivos podem incluir o seguinte: firewalls, prevenção de negação distribuída de serviço (DDoS), detecção de intrusos ou sistemas de proteção contra (IDS/IPS) e dispositivos VPN. Aplicação da política pode levar a forma de diretivas de firewall, listas de controle de acesso (ACLs) ou roteamento específico. A primeira linha de defesa da rede, diretamente aceitar tráfego de entrada da Internet, é uma combinação desses mecanismos para bloquear ataques e tráfego prejudicial permitindo solicitações legítimas ainda mais na rede. Esse tráfego roteia diretamente para recursos na rede de perímetro. Esse recurso pode, em seguida, "falar" para os recursos mais profundas da rede, transiting limite da próxima de validação primeiro. A camada mais externa é chamada rede de perímetro porque essa parte da rede é exposta à Internet, geralmente com alguma forma de proteção em ambos os lados. A figura a seguir mostra um exemplo de uma rede de perímetro única sub-rede em uma rede corporativa, com dois limites de segurança.

![Uma rede de perímetro em uma rede corporativa][3]

Há muitas arquiteturas usadas para implementar uma rede de perímetro de um balanceador de carga simples na frente de uma web farm, a uma rede de perímetro sub-rede vários com mecanismos variadas em cada limite para bloquear o tráfego e proteger as camadas mais profundas da rede corporativa. Como a rede de perímetro é criada depende de necessidades específicas da organização e sua tolerância de risco geral.

Como os clientes mover as cargas de trabalho para nuvens públicas, é essencial para oferecer suporte a recursos semelhantes para arquitetura de rede de perímetro no Azure para atender aos requisitos de segurança e conformidade. Este documento fornece diretrizes sobre como os clientes podem criar um ambiente de rede seguro no Azure. Ele se concentra na rede de perímetro, mas também inclui uma discussão abrangente de muitos aspectos de segurança de rede. As seguintes perguntas informam essa discussão:

- Como uma rede de perímetro no Azure pode ser criada?
- Quais são alguns dos recursos disponíveis para criar a rede de perímetro Azure?
- Como cargas de trabalho de back-end podem ser protegidas?
- Como as comunicações da Internet são controladas para as cargas de trabalho no Azure?
- Como as redes locais podem ser protegidas de implantações no Azure?
- Quando os recursos de segurança do Azure nativo devem ser usados em vez de dispositivos de terceiros ou serviços?

O diagrama a seguir mostra várias camadas de segurança que Azure fornece aos clientes. Essas camadas são nativo na plataforma Windows Azure em si e recursos definidos pelo cliente:

![Arquitetura de segurança do Azure][4]

Entrada da Internet, DDoS Azure ajuda a proteger contra ataques em grande escala do Azure. A próxima camada é definido pelo cliente pontos de extremidade públicos, que são usados para determinar qual tráfego pode passar através do serviço de nuvem para a rede virtual. Azure nativo virtual isolamento da rede garante isolamento completo de todas as outras redes, e que o tráfego somente flui através de métodos e caminhos de usuário configurado. Estes caminhos e métodos são a próxima camada, onde o NSGs, UDR e dispositivos virtuais de rede podem ser utilizados para criar limites de segurança para proteger as implantações de aplicativos na rede protegida.

A próxima seção fornece uma visão geral do Azure redes virtuais. Essas redes virtuais são criados por clientes e são o que as cargas de trabalho implantadas estão conectadas aos. Redes virtuais são a base de todos os recursos de segurança de rede necessários para estabelecer uma rede de perímetro para proteger implantações de clientes no Azure.

## <a name="overview-of-azure-virtual-networks"></a>Visão geral do Azure redes virtuais
Antes do tráfego de Internet pode acessar as redes virtuais Azure, há duas camadas de segurança inerentes à plataforma Windows Azure:

1.  **Proteção de DDoS**: proteção de DDoS é uma camada da rede física Azure que protege a plataforma Windows Azure próprio de grande escala ataques baseados na Internet. Esses ataques usam vários nós "bot" em uma tentativa para sobrecarregar um serviço de Internet. Azure tem uma proteção DDoS robusta mescla em toda a conectividade Internet entrada. Essa camada de proteção de DDoS não tem usuário configurável atributos e não é acessível para o cliente. Isso protege Azure como uma plataforma de ataques em grande escala, mas não irá proteger diretamente o aplicativo cliente individual. Camadas adicionais de resiliência podem ser configuradas pelo cliente contra um ataque localizado. Por exemplo, se um cliente foi atacado com um ataque de DDoS grande escala em um ponto de extremidade público, Azure bloqueia conexões nesse serviço. Cliente poderia alternar para outra rede virtual ou ponto de extremidade não envolvidas ataque para restaurar o serviço do serviço. Deve-se observar que embora cliente pode ser afetado no ponto de extremidade, nenhum outro serviço fora desse ponto de extremidade seria afetado. Além disso, outros clientes e serviços não verá nenhum impacto desse ataque.
2.  **Pontos de extremidade do serviço**: pontos de extremidade permitem nuvem grupos de serviços ou recurso tenha públicas endereços Internet IP e portas expostas. O ponto de extremidade usará a conversão de endereço de rede (NAT) para rotear o tráfego para o endereço interno e a porta em uma rede virtual Azure. Este é o caminho principal de tráfego externo passar para a rede virtual. Os pontos de extremidade do serviço são configurável pelo usuário para determinar qual tráfego é passado e como e onde ele é convertido em uma rede virtual.

Quando o tráfego atinge a rede virtual, há vários recursos que entram. Redes virtuais Azure são a base de clientes anexar as cargas de trabalho e onde aplica a segurança em nível de rede básica. É uma rede privada (uma sobreposição de rede virtual) no Azure para clientes com os seguintes recursos e características:
 
- **Isolamento de tráfego**: uma rede virtual é o limite de isolamento de tráfego na plataforma Windows Azure. Máquinas virtuais (VMs) em uma rede virtual não conseguir se comunicar diretamente para VMs em uma rede virtual diferente, mesmo se ambas as redes virtuais são criadas pelo mesmo cliente. Esta é uma propriedade crítica que garante VMs de cliente e comunicação permaneça privada dentro de uma rede virtual.
- **Topologia de várias camadas**: redes virtuais permitem aos clientes definir topologia de várias camadas alocar sub-redes e designando espaços de endereço separadas para diferentes elementos ou "níveis" de suas cargas de trabalho. Esses agrupamentos lógicos topologias permitem que os clientes definir a política de acesso diferentes com base nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas.
- **Conectividade entre locais**: clientes podem estabelecer conectividade entre locais entre uma rede virtual e vários sites locais ou outras redes virtuais no Azure. Para fazer isso, os clientes podem usar Azure VPN Gateways ou dispositivos virtuais de rede de terceiros. Azure suporta-to-site (S2S) VPNs usando protocolos IKE/IPsec padrão e rota expressa particular conectividade.
- **NSG** permite aos clientes criar regras (ACLs) o nível desejado de detalhamento: interfaces, VMs individuais ou sub-redes virtuais de rede. Os clientes podem controlar o acesso ao permitir ou negar a comunicação entre as cargas de trabalho em uma rede virtual, sistemas de em redes do cliente por meio de conectividade entre locais, ou direcionar comunicação da Internet.
- **Encaminhamento de IP** e **UDR** permitem aos clientes definir os caminhos de comunicação entre níveis diferentes dentro de uma rede virtual. Clientes podem implantar um firewall, IDS/IPS e outros dispositivos virtuais e rotear o tráfego de rede por meio desses dispositivos de segurança para aplicação de políticas de limite de segurança, auditoria e inspeção.
- **Dispositivos de rede virtuais** do Azure Marketplace: dispositivos de segurança como firewalls, balanceadores de carga e IDS/IPS estão disponíveis no mercado do Azure e a Galeria de imagens de máquina virtual. Os clientes podem implantar esses eletrodomésticos em suas redes virtuais e especificamente, em seus limites de segurança (incluindo as sub-redes da rede de perímetro) para concluir um ambiente de rede seguro de várias camadas.

Com esses recursos e funcionalidades, um exemplo de como uma arquitetura de rede de perímetro pode ser construída no Azure é o seguinte:

![Uma rede de perímetro em uma rede virtual Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisitos e características de rede de perímetro
Rede de perímetro é projetada para ser o front-end da rede, diretamente interface comunicação da Internet. Os pacotes de entrada deverão fluem os dispositivos de segurança, como o firewall, IDS e IPS, antes de contatar os servidores de back-end. Pacotes de Internet ligadas de cargas de trabalho também podem fluir pelos dispositivos de segurança na rede de perímetro para aplicação da política, inspeção e fins de auditoria, antes de sair da rede. Além disso, a rede de perímetro pode hospedar gateways VPN entre locais entre redes virtual do cliente e locais.

### <a name="perimeter-network-characteristics"></a>Características de rede de perímetro
Algumas das características de uma rede de perímetro boa referenciando figura anterior, são da seguinte maneira:

- Voltado para a Internet:
    - A própria sub-rede de rede de perímetro é voltado para a Internet, comunicar-se diretamente com a Internet.
    - IPs público, VIPs e/ou pontos de extremidade do serviço passam tráfego de Internet para a rede front-end e dispositivos.
    - O tráfego da Internet passa por dispositivos de segurança antes de outros recursos na rede front-end.
    - Se a segurança de saída estiver habilitada, o tráfego passa por meio de dispositivos de segurança, como a etapa final, antes de passar para a Internet.
- Rede protegida:
    - Há um caminho direto da Internet na infraestrutura de núcleo.
    - Canais para a infraestrutura de núcleo devem percorrer por meio de dispositivos de segurança como NSGs, firewalls ou dispositivos VPN.
    - Outros dispositivos não devem ponte Internet e a infraestrutura de núcleo.
    - Dispositivos de segurança sobre o voltado para a Internet e da rede protegida opostas limites da rede de perímetro (por exemplo, os dois firewall ícones mostrados na figura anterior), na verdade, podem ser um único dispositivo virtual com regras diferenciadas ou interfaces para cada limite. (Ou seja, um dispositivo, logicamente separado, lidar com a carga de ambos os limites da rede de perímetro.)
- Práticas recomendadas e outras restrições comuns:
    - Cargas de trabalho não devem armazenar informações essenciais de negócios.
    - Acesso e atualizações as configurações de rede de perímetro e implantações limitam-se somente aos administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de rede de perímetro
Para habilitar essas características, siga estas diretrizes sobre os requisitos de rede virtual para implementar uma rede de perímetro bem-sucedido:

- **Arquitetura de sub-rede:** Especifique a rede virtual, de forma que uma sub-rede inteira está empenhada como rede de perímetro, separada de outras sub-redes na mesma rede virtual. Isso garante que o tráfego entre a rede de perímetro e outros fluxos de níveis de sub-rede interna ou privada por meio de um firewall ou dispositivo de virtual IDS/IPS os limites de sub-rede com rotas definidas pelo usuário.
- **NSG:** A própria sub-rede de rede de perímetro deve estar aberta para permitir a comunicação com a Internet, mas isso não significa que os clientes devem ser ignorando NSGs. Siga as práticas de segurança comuns para minimizar as emerge as de rede expostas na Internet. Bloquear os intervalos de endereço remoto permitidos para acessar as implantações ou os protocolos de aplicativo específico e portas abertas. Pode haver circunstâncias, no entanto, em que isso nem sempre é possível. Por exemplo, se os clientes têm um site externo no Azure, a rede de perímetro deve permitir que as solicitações de web de entrada de endereços IP públicos, mas só deve abrir as portas do aplicativo web: TCP:80 e TCP:443.
- **Tabela de roteamento:** A própria sub-rede de rede de perímetro deve ser capaz de se comunicar diretamente com a Internet, mas não deve permitir a comunicação direta de e para as redes back end ou local sem passar por um dispositivo de segurança ou firewall.
- **Configuração de dispositivo de segurança:** Para rotear e inspecionar pacotes entre a rede de perímetro e o restante das redes protegidas, os dispositivos de segurança como firewall, IDS e IPS dispositivos podem ser hospedagem múltipla. Eles podem ter NICs separadas para a rede de perímetro e as sub-redes de back-end. As NICs na rede de perímetro se comunicar diretamente e da Internet, com os NSGs correspondentes e a tabela de roteamento de rede de perímetro. As NICs conectando às sub-redes de back-end têm mais restrito NSGs e tabelas de roteamento das sub-redes back-end correspondentes.
- **Funcionalidade de dispositivo de segurança:** Os dispositivos de segurança implantados na rede de perímetro normalmente executam as seguintes funções:
    - Firewall: Impondo regras de firewall ou diretivas de controle de acesso para as solicitações de entrada.
    - Detecção e prevenção de ameaças: detectar e atenuar ataques da Internet.
    - Auditoria e log: manter logs detalhados para análise e auditoria.
    - Proxy reverso: redirecionando as solicitações de entrada para os servidores de back-end correspondentes. Isso envolve mapeamento e convertendo os endereços de destino nos dispositivos de front-end, firewalls normalmente, para os endereços de servidor back-end.
    - Encaminhar proxy: fornecendo NAT e a execução de auditoria para a comunicação iniciada de dentro da rede virtual à Internet.
    - Roteador: Encaminhando tráfego de entrada e entre sub-rede dentro da rede virtual.
    - Dispositivo VPN: atuando como os gateways VPN entre locais para conectividade VPN entre locais entre redes de local do cliente e Azure virtual.
    - Servidor VPN: aceitando clientes VPN conectando ao Azure redes virtuais.

>[AZURE.TIP] Manter dois grupos a seguir separados: os indivíduos autorizados a acessar a engrenagem de segurança de rede de perímetro e os indivíduos autorizados como administradores de desenvolvimento, implantação ou operações de aplicativo. Manter esses grupos separados permite uma diferenciação de direitos e impede que uma única pessoa ignorando segurança de aplicativos e controles de segurança de rede.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Perguntas a ser solicitado ao criar limites de rede
Nesta seção, a menos que especificamente mencionado, o termo "redes" se refere a privada redes virtuais Azure criadas por um administrador de assinatura. O termo não se referir às redes físicas subjacentes dentro do Azure.

Além disso, redes virtuais Azure muitas vezes são usadas para estender redes tradicionais no local. É possível incorporar-to-site ou soluções de rede rota expressa híbrida com arquiteturas de rede de perímetro. Essa é uma consideração importante na criação de limites de segurança de rede.

As três perguntas a seguintes são fundamentais para atender quando você estiver criando uma rede com uma rede de perímetro e vários limites de segurança.

#### <a name="1-how-many-boundaries-are-needed"></a>1) limites de quantos são necessários?
O primeiro ponto de decisão é decidir quantos limites de segurança são necessárias em um determinado cenário:

- Um único limite: uma na rede de perímetro front-end, entre a rede virtual e a Internet.
- Dois limites: um Internet do lado da rede de perímetro e outro entre sub-rede da rede de perímetro e as sub-redes de back-end em redes virtuais Azure.
- Limites de três: um lado Internet da rede de perímetro, um entre a rede de perímetro e sub-redes de back-end e uma entre as sub-redes de back-end e da rede local.
- Limites de N: um número variável. Dependendo de requisitos de segurança, há realmente qualquer número de limites de segurança que podem ser aplicadas em uma determinada rede.

O número e tipo dos limites de necessário variará com base em tolerância de risco da empresa e do cenário específico sendo implementado. Geralmente, essa é uma decisão joint feita por vários grupos dentro de uma organização, incluindo geralmente um risco e equipe de conformidade, uma rede e equipe da plataforma e uma equipe de desenvolvimento de aplicativos. Pessoas com conhecimento de segurança, os dados envolvidos e as tecnologias sendo usadas devem ter um digamos nesta decisão para garantir a postura de segurança apropriadas para cada implementação.

>[AZURE.TIP] Use o menor número de limites que satisfazer os requisitos de segurança para uma determinada situação. Com mais limites mais difícil operações e solução de problemas podem ser, bem como a sobrecarga de gerenciamento envolvida Gerenciando as políticas de limite vários ao longo do tempo. No entanto, limites insuficientes aumentam o risco. Encontrar o equilíbrio é fundamental.

![Rede de híbrido com três limites de segurança][6]

A figura anterior mostra uma visão geral de uma rede de limite de três segurança. Os limites são entre a rede de perímetro e Internet, Azure front-end e back-end particulares sub-redes e a Azure sub-rede de back-end e a rede corporativa no local.

#### <a name="2-where-are-the-boundaries-located"></a>2) onde estão localizados os limites?
Depois que o número de limites é decidido, onde implementá-los é o próximo ponto de decisão. Em geral, há três opções:
- Usando um serviço intermediário baseados na Internet (por exemplo, um baseado em nuvem Web application firewall, que não é discutido neste documento)
- Usando recursos nativos e/ou dispositivos de rede virtual no Azure
- Usando dispositivos físicos na rede local

Em redes puramente Azure, as opções são recursos nativos Azure (por exemplo, balanceadores de carga de Azure) ou rede dispositivos virtuais de ecossistema de parceiros rico do Azure (por exemplo, firewalls de ponto de verificação).

Se for necessário um limite entre Azure e uma rede local, os dispositivos de segurança podem residem em ambos os lados da conexão (ou ambos os lados). Portanto, uma decisão deve ser feita no local para colocar o engrenagem de segurança.

Na figura anterior, a rede de Internet para perímetro os limites de front-to-back-end totalmente contidos Azure em devem ser recursos Azure nativos ou rede dispositivos virtuais. Dispositivos de segurança no limite entre Azure (back-end sub-rede) e a rede corporativa podem ser no lado do Azure ou lado local ou até mesmo uma combinação de dispositivos nos dois lados. Pode haver significativas vantagens e desvantagens em qualquer uma das opções que devem ser consideradas seriamente.

Por exemplo, usar engrenagem de segurança física existente no lado da rede local tem a vantagem que nenhuma nova engrenagem é necessária. Ela só precisa reconfiguração. A desvantagem, no entanto, é que todo o tráfego deve voltar do Azure à rede local a ser visto pela engrenagem de segurança. Portanto, o tráfego do Azure para Azure poderia provoca latência significativa e afetam o desempenho do aplicativo e experiência de usuário, se ele foi forçado novamente à rede local para a imposição de política de segurança.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) como os limites são implementados?
Cada limite de segurança terá que requisitos de capacidade diferente (por exemplo, Identificações e regras de firewall do lado de Internet da rede perímetro, mas somente ACLs entre a rede de perímetro e sub-rede de back-end). Decidir quais dispositivos usar depende dos requisitos de segurança e cenário. Na seção a seguir, exemplos 1, 2 e 3 discutam algumas opções que podem ser usadas. Examinar os recursos de rede nativo Azure e os dispositivos disponíveis no Azure do ecossistema do parceiro mostra as opções inúmeras disponíveis para resolver praticamente qualquer cenário.

Outro ponto de decisão de implementação de chave é como conectar-se a rede local com o Azure. Você deve usar o gateway virtual Azure ou um dispositivo virtual de rede? Essas opções são discutidas mais detalhadamente na seção a seguir (exemplos 4, 5 e 6).

Além disso, o tráfego entre redes virtuais dentro do Azure poderão ser necessárias. Esses cenários serão adicionados em uma data posterior.

Depois que você tiver as respostas para as perguntas anteriores, a seção [Fast Start](#fast-start) pode ajudar a identificar quais exemplos são mais apropriados para um determinado cenário.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemplos: Limites de segurança de construção com redes virtuais Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemplo 1: Criar uma rede de perímetro para ajudar a proteger aplicativos com NSGs
[Voltar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example1]

![Rede de perímetro com NSG de entrada][7]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, "CorpNetwork", com duas sub-redes: "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Windows server que representa um aplicativo de servidor de web ("IIS01")
- Dois servidores Windows que representam servidores de back-end do aplicativo ("AppVM01", "AppVM02")
- Windows server que representa um servidor DNS ("DNS01")

Para scripts e um modelo do Gerenciador de recursos do Azure, consulte as [instruções detalhadas de compilação][Example1].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com seis regras.

>[AZURE.TIP] Em geral, você deve criar regras específicas "Permitir" primeiro, o seguido por regras "Negar" mais genéricas. A prioridade de determinado determina quais regras são avaliadas primeiro. Quando o tráfego está localizado para aplicar a uma regra específica, não há mais regras são avaliadas. Regras NSG podem aplicar a direção entrada ou saída (a partir da perspectiva da sub-rede).

Forma declarativa, as regras a seguir estão sendo criadas para tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido.
2.  Tráfego RDP (porta 3389) da Internet em qualquer máquina Virtual é permitido.
3.  Tráfego HTTP (porta 80) da Internet para o servidor de web (IIS01) é permitido.
4.  Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5.  Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6.  Qualquer tráfego (todas as portas) da sub-rede front-end à sub-rede back-end é negado.

Com estas regras vinculado a cada sub-rede, se uma solicitação HTTP foi entrada da Internet para o servidor web, ambas as regras 3 (Permitir) e 5 (Negar) seria aplicado. Mas porque regra 3 tem uma prioridade mais alta, ele seria aplicam-se apenas e regra 5 prefere não entram. Portanto, a solicitação HTTP seria ter permissão para o servidor da web. Se esse mesmo tráfego estava tentando alcançar o servidor de DNS01, regra 5 (Negar) seria o primeiro a aplicar e o tráfego não deve ter permissão para passar para o servidor. Regra de 6 (Negar) bloqueia a sub-rede front-end do falando à sub-rede back-end (exceto para tráfego permitido em regras 1 e 4). Isso protege a rede de back-end no caso de um invasor compromete o aplicativo da web no front-end. O invasor poderia ter acesso limitado à rede "protegida" back-end (somente para recursos expostos no servidor AppVM01).

Não há uma regra de saída do padrão que permite tráfego check-out para a Internet. Neste exemplo, estamos permitir o tráfego de saída e não modificando as regras de saída. Para bloquear o tráfego nas duas direções, definidos pelo usuário roteamento é necessário (consulte exemplo 3).

#### <a name="conclusion"></a>Conclusão
Esta é uma maneira relativamente simple e direta de isolar a sub-rede de back-end de tráfego de entrada. Para obter mais informações, consulte as [instruções detalhadas de compilação][Example1]. Essas instruções incluem:

- Como criar esta rede de perímetro com scripts do PowerShell.
- Como criar esta rede de perímetro com um modelo do Gerenciador de recursos do Azure.
- Descrições detalhadas de cada comando NSG.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego é permitido ou negado em cada camada.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: Criar uma rede de perímetro para ajudar a proteger aplicativos com um firewall e NSGs
[Voltar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example2]

![Rede de perímetro com NVA e NSG de entrada][8]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, "CorpNetwork", com duas sub-redes: "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Um dispositivo virtual de rede, neste caso uma firewall, conectada à sub-rede front-end
- Windows server que representa um aplicativo de servidor de web ("IIS01")
- Dois servidores Windows que representam servidores de back-end do aplicativo ("AppVM01", "AppVM02")
- Windows server que representa um servidor DNS ("DNS01")

Para scripts e um modelo do Gerenciador de recursos do Azure, consulte as [instruções detalhadas de compilação][Example2].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com seis regras.

>[AZURE.TIP] Em geral, você deve criar regras específicas "Permitir" primeiro, o seguido por regras "Negar" mais genéricas. A prioridade de determinado determina quais regras são avaliadas primeiro. Quando o tráfego está localizado para aplicar a uma regra específica, não há mais regras são avaliadas. Regras NSG podem aplicar a direção entrada ou saída (a partir da perspectiva da sub-rede).

Forma declarativa, as regras a seguir estão sendo criadas para tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido.
2.  Tráfego RDP (porta 3389) da Internet em qualquer máquina Virtual é permitido.
3.  Qualquer tráfego de Internet (todas as portas) para o dispositivo de rede virtual (firewall) é permitido.
4.  Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5.  Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6.  Qualquer tráfego (todas as portas) da sub-rede front-end à sub-rede back-end é negado.

Com estas regras vinculado a cada sub-rede, se uma solicitação HTTP foi entrada da Internet ao firewall, ambas as regras 3 (Permitir) e 5 (Negar) seria aplicado. Mas porque regra 3 tem uma prioridade mais alta, ele seria aplicam-se apenas e regra 5 prefere não entram. Portanto, a solicitação HTTP seria ter permissão para o firewall. Se esse mesmo tráfego estava tentando alcançar o servidor de IIS01, mesmo que ele esteja na sub-rede front-end, regra 5 (Negar) seria aplicado, e o tráfego não deve ser permitido para passar para o servidor. Regra de 6 (Negar) bloqueia a sub-rede front-end do falando à sub-rede back-end (exceto para tráfego permitido em regras 1 e 4). Isso protege a rede de back-end no caso de um invasor compromete o aplicativo da web no front-end. O invasor poderia ter acesso limitado à rede "protegida" back-end (somente para recursos expostos no servidor AppVM01).

Não há uma regra de saída do padrão que permite tráfego check-out para a Internet. Neste exemplo, estamos permitir o tráfego de saída e não modificando as regras de saída. Para bloquear o tráfego nas duas direções, definidos pelo usuário roteamento é necessário (consulte exemplo 3).

#### <a name="firewall-rule-description"></a>Descrição da regra de firewall
No firewall, regras de encaminhamento devem ser criada. Como este exemplo apenas rotear o tráfego de Internet no ligadas ao firewall e, em seguida, para o servidor web, encaminhamento de apenas uma rede NAT (NAT) regra é necessário.

A regra de encaminhamento aceita qualquer endereço de entrada de origem que acessa o firewall tentando alcançar HTTP (porta 80 ou 443 para HTTPS). Ela tem enviado fora interface local do firewall e redirecionado para o servidor web com o endereço IP do 10.0.1.5.

#### <a name="conclusion"></a>Conclusão
Essa é uma maneira relativamente simples de proteger seu aplicativo com um firewall e isolar a sub-rede de back-end de tráfego de entrada. Para obter mais informações, consulte as [instruções detalhadas de compilação][Example2]. Essas instruções incluem:

- Como criar esta rede de perímetro com scripts do PowerShell.
- Como criar esse exemplo com um modelo do Gerenciador de recursos do Azure.
- Descrições detalhadas de cada regra de comando e o firewall NSG.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego é permitido ou negado em cada camada.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3: Criar uma rede de perímetro para ajudar a proteger as redes com um firewall, UDR e NSG
[Voltar ao início rápido](#fast-start) | [detalhado construir instruções para este exemplo][Example3]

![Rede de perímetro bidirecional com NVA, NSG e UDR][9]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Três serviços em nuvem: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Uma rede virtual, "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
- Um dispositivo virtual de rede, neste caso uma firewall, conectada à sub-rede SecNet
- Windows server que representa um aplicativo de servidor de web ("IIS01")
- Dois servidores Windows que representam servidores de back-end do aplicativo ("AppVM01", "AppVM02")
- Windows server que representa um servidor DNS ("DNS01")

Para scripts e um modelo do Gerenciador de recursos do Azure, consulte as [instruções detalhadas de compilação][Example3].

#### <a name="udr-description"></a>Descrição de UDR
Por padrão, as seguintes rotas de sistema são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

O VNETLocal é sempre a prefix(es) de endereço definidos da rede virtual para essa rede específico (ou seja, ele alterada de rede virtual à rede virtual, dependendo de como cada rede virtual específico é definido). As rotas de sistema restantes são estáticas e padrão conforme indicado na tabela.

Neste exemplo, duas tabelas de roteamento são criados, cada um para as sub-redes front-end e back-end. Cada tabela é carregada com rotas estáticas apropriadas para a sub-rede determinada. Nesse exemplo, cada tabela tem três rotas que direcionar todo o tráfego (0.0.0.0/0) através do firewall (próximo salto = endereço IP de dispositivo Virtual):

1. Tráfego de sub-rede local com nenhum próximo salto definido para permitir o tráfego de sub-rede local para ignorar o firewall.
2. Tráfego de rede virtual com um próximo nó definido como firewall; Isso substitui a regra padrão que permite o tráfego de rede virtual local rotear diretamente.
3. Todas as restante tráfego (0/0) com um próximo nó definido como o firewall.

>[AZURE.TIP] Não ter a entrada de sub-rede local no UDR interromperá comunicações de sub-rede local. 
> - Em nosso exemplo, 10.0.1.0/24 apontando para VNETLocal é essencial como caso contrário, pacote deixando o servidor da Web (10.0.1.4) destinados para outro servidor local (por exemplo) 10.0.1.25 falhará conforme eles serão enviados para NVA, que enviará-lo à sub-rede, e a sub-rede será novamente enviá-lo para o NVA e assim por diante.
> - Chances de um loop de roteamento são geralmente mais altos em dispositivos de multi-nic que estão diretamente conectados a cada sub-rede eles estão comunicando com, que geralmente é do tradicional, local, eletrodomésticos. 

Depois que as tabelas de roteamento são criadas, eles são vinculados a suas sub-redes. A tabela de roteamento sub-rede front-end, uma vez criado e vinculado à sub-rede, teria esta aparência:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR agora pode ser aplicada à sub-rede gateway no qual o circuito rota expressa está conectado.
>
> Exemplos de como ativar sua rede de perímetro com rota expressa ou rede to-site são mostrados nos exemplos 3 e 4.


#### <a name="ip-forwarding-description"></a>Descrição de encaminhamento de IP
Encaminhamento de IP é um recurso companion UDR. Esta é uma configuração em um dispositivo virtual que permite que ela receba o tráfego não especificamente endereçado ao aparelho e encaminhar que o tráfego para seu destino final.

Por exemplo, se o tráfego de AppVM01 faz uma solicitação para o servidor DNS01, UDR seria rotear isso ao firewall. Com o encaminhamento de IP habilitado, o tráfego para o destino de DNS01 (10.0.2.4) é aceito pelo dispositivo (10.0.0.4) e então encaminhado para seu destino final (10.0.2.4). Sem IP Forwarding habilitadas no firewall, o tráfego não serão aceitos pelo aparelho mesmo que a tabela de rota tem o firewall como o salto seguinte. Para usar um dispositivo virtual, é fundamental lembrar de habilitar o encaminhamento de IP em conjunto com UDR.

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com uma única regra. Este grupo é associado somente às sub-redes front-end e back-end (não a SecNet). Forma declarativa a seguinte regra está sendo criada:

- Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (todas as sub-redes) é negado.

Embora NSGs são usados neste exemplo, sua finalidade principal é como uma camada secundária de defesa contra configuração manual. O objetivo é bloquear todo o tráfego da Internet para as sub-redes front-end ou back-end. Tráfego que deve fluir somente através da sub-rede SecNet ao firewall (e, se apropriado, as sub-redes front-end ou back-end-lo). Além disso, com as regras UDR no lugar, qualquer tráfego que fez para as sub-redes front-end ou back-end seria direcionado check-out para o firewall (graças ao UDR). O firewall poderia ser visto como um fluxo de assimétrico e seria solte o tráfego de saída. Portanto, há três camadas de segurança protegendo as sub-redes:
- Nenhum ponto de extremidade aberto no FrontEnd001 e BackEnd001 serviços de nuvem.
- NSGs negar o tráfego da Internet.
- O tráfego de assimétricos de queda de firewall.

Um ponto interessante sobre o NSG neste exemplo é que ela contém apenas uma regra, o que é negar tráfego de Internet para a rede virtual inteiro, incluindo a sub-rede de segurança. No entanto, como o NSG está ligado apenas para as sub-redes front-end e back-end, a regra não é processada no tráfego de entrada para a sub-rede de segurança. Como resultado, o tráfego fluirá para a sub-rede de segurança.

#### <a name="firewall-rules"></a>Regras de firewall
No firewall, regras de encaminhamento devem ser criada. Como o firewall está bloqueando ou encaminhamento tudo entrado, saído e tráfego de rede entre virtual, muitas regras de firewall são necessários. Além disso, todo o tráfego atingirá o endereço IP público do serviço de segurança (em diferentes portas), para ser processada pelo firewall. Uma prática recomendada é criar um diagrama os fluxos de lógicos antes de configurar as sub-redes e regras de firewall, para evitar reformular mais tarde. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:
 
![Exibição lógica das regras de firewall][10]

>[AZURE.NOTE] Com base no dispositivo Virtual rede usado, as portas de gerenciamento variam. Neste exemplo, um Firewall de NextGen Barracuda é referenciado, que usa portas 22, 801 e 807. Consulte a documentação do fornecedor do dispositivo para localizar as portas exatas usadas para o gerenciamento do dispositivo sendo usado.

#### <a name="firewall-rules-description"></a>Descrição de regras de firewall
No diagrama lógico anterior, a sub-rede de segurança não é mostrada. Isso ocorre porque o firewall é o único recurso nessa sub-rede e este diagrama mostra as regras de firewall e como eles logicamente permitem ou negar fluxos de tráfego, não o caminho roteado real. Além disso, as portas externas selecionadas para o tráfego RDP são mais altos intervalo de portas (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para fins de legibilidade fácil (por exemplo, o endereço de servidor local 10.0.1.4 está associado a porta externa 8014). No entanto, qualquer portas superiores não-conflitante, podem ser usadas.

Neste exemplo, precisamos sete tipos de regras:

- Regras externas (para tráfego de entrada):
  1.    Regra de gerenciamento de firewall: este aplicativo redirecionar regra permite o tráfego passar para as portas de gerenciamento do aparelho rede virtual.
  2.    Regras RDP (para cada Windows server): estes quatro regras (uma para cada servidor) permitirem o gerenciamento dos servidores individuais via RDP. Isso também poderia ser agrupado em uma regra, dependendo dos recursos do dispositivo virtual rede sendo usado.
  3.    Regras de tráfego de aplicativo: há dois dessas regras, a primeira para o tráfego de web front-end e o segundo para o tráfego de back-end (por exemplo, o servidor web para o nível de dados). A configuração dessas regras depende da arquitetura de rede (onde seus servidores são colocadas) e fluxos de tráfego (direção na qual os fluxos de tráfego e quais portas são usadas).
      - A primeira regra permite o tráfego de aplicativo real para acessar o servidor de aplicativo. Enquanto outras regras permitirem segurança e gerenciamento, regras de tráfego do aplicativo são o que permite que usuários externos ou serviços para acessar os aplicativos. Neste exemplo, há um único servidor web na porta 80. Portanto, uma regra de aplicativo de firewall único redireciona o tráfego de entrada para o IP externo, o endereço IP interno do servidores de web. A sessão de tráfego redirecionado deve ser convertida via NAT para o servidor interno.
      - A segunda regra é a regra de back-end para permitir que o servidor da web para se comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer porta.
- Regras internas (para tráfego de rede entre virtual)
  4.    Saída a regra de Internet: esta regra permite o tráfego de qualquer rede para passar para redes selecionadas. Essa regra é geralmente uma regra padrão já no firewall, mas em um estado desabilitado. Essa regra deve ser habilitada para esse exemplo.
  5.    Regra DNS: esta regra permite apenas o tráfego DNS (porta 53) passar para o servidor DNS. Para esse ambiente mais tráfego de front-end para back-end está bloqueado. Essa regra permite especificamente DNS de qualquer sub-rede local.
  6.    Sub-rede a regra de sub-rede: esta regra é permitir que qualquer servidor na sub-rede back-end para se conectar a qualquer servidor na sub-rede front-end (mas não o inverso).
- Regra de falhas (para o tráfego que não atende a qualquer uma das anterior):
  7.    Negar todas as regras de tráfego: isso sempre deve estar na regra final (em termos de prioridade) e como tal se um fluxo de tráfego não correspondem a nenhum dos anterior regras a ele será cancelado por esta regra. Esta é uma regra padrão e costuma ser ativado. Sem modificações geralmente são necessários.

>[AZURE.TIP] Sobre a segunda regra de tráfego de aplicativo, para simplificar neste exemplo, qualquer porta é permitida. Em um cenário real, a porta mais específica e intervalos de endereços devem ser usados para reduzir a superfície de ataque dessa regra.

Depois que todas as regras anteriores são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme desejado. Neste exemplo, as regras são em ordem de prioridade.

#### <a name="conclusion"></a>Conclusão
Essa é uma maneira mais complexa, mas mais completa de proteção e isolar a rede que os exemplos anteriores. (Exemplo 2 protege apenas o aplicativo e exemplo 1 apenas isola sub-redes). Este design permite monitorar o tráfego nas duas direções e protege não apenas o servidor de entrada do aplicativo, mas impõe a política de segurança de rede para todos os servidores da rede. Além disso, dependendo do dispositivo usado, auditoria de tráfego completo e percepção podem ser obtidos. Para obter mais informações, consulte as [instruções detalhadas de compilação][Example3]. Essas instruções incluem:

- Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
- Como criar esse exemplo com um modelo do Gerenciador de recursos do Azure.
- Detalhadas descrições de cada UDR, NSG comando e regra de firewall.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego é permitido ou negado em cada camada.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Exemplo 4: Adicionar uma conexão de híbrido com uma dispositivo virtual-to-site rede virtual privada (VPN)
[Voltar ao início rápido](#fast-start) | Construir obter instruções detalhadas disponíveis em breve

![Rede de perímetro com NVA conectados rede híbrido][11]

#### <a name="environment-description"></a>Descrição do ambiente
Rede de híbrido usando um dispositivo de rede virtual (NVA) pode ser adicionada a qualquer um dos tipos de rede de perímetro descritos nos exemplos 1, 2 ou 3.

Como mostrado na figura anterior, uma conexão VPN pela Internet (-to-site) é usado para conectar uma rede local para uma rede virtual Azure por meio de um NVA.

>[AZURE.NOTE] Se você usar rota expressa com a opção de correspondência pública do Azure habilitada, uma rota estática deve ser criada. Isso deve rotear para o endereço de IP de VPN de NVA seu corporativo na Internet e não por meio de WAN a rota expressa. O NAT necessário na opção rota expressa do Azure público correspondência pode interromper a sessão VPN.

Depois que a VPN estiver no lugar, o NVA se torna o hub central para todas as redes e sub-redes. As regras de encaminhamento de firewall determinam quais fluxos de tráfego são permitidos, são convertidas pelo NAT, serão redirecionadas ou são descartadas (mesmo para fluxos de tráfego entre a rede local e o Azure).

Fluxos de tráfego devem ser considerados cuidadosamente, pois elas podem ser otimizadas ou degradados por esse padrão de design, dependendo do específicos do caso de uso.

Usando o ambiente integrado exemplo 3 e, em seguida, adicionar uma conexão de rede-to-site VPN híbridos, produz o design a seguir:

![Rede de perímetro com NVA conectados usando uma VPN to-site][12]

O roteador local ou qualquer outro dispositivo de rede que seja compatível com sua NVA para VPN, seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a conexão VPN com seu NVA.

Logicamente para NVA, a rede parece separado quatro "zonas de segurança" com as regras sobre o NVA sendo Diretor principal de tráfego entre essas zonas:

![Rede lógica perspectiva NVA][13]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede-to-site VPN híbrido para uma rede virtual Azure pode estender a rede local para Azure de maneira segura. Ao usar uma conexão VPN, o tráfego está criptografado e roteia através da Internet. O NVA neste exemplo fornece um local central para aplicar e gerenciar a política de segurança. Para obter mais informações, consulte as instruções de compilação detalhadas (em breve). Essas instruções incluem:

- Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
- Como criar esse exemplo com um modelo do Gerenciador de recursos do Azure.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego flui através deste design.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Exemplo 5: Adicionar uma conexão de híbrido com um gateway-to-site, Azure VPN
[Voltar ao início rápido](#fast-start) | Construir obter instruções detalhadas disponíveis em breve

![Rede de perímetro com a rede do gateway híbrido conectada][14]

#### <a name="environment-description"></a>Descrição do ambiente
Rede híbrido usando um gateway VPN Azure pode ser adicionada a qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Como mostrado na figura anterior, uma conexão VPN pela Internet (-to-site) é usado para conectar uma rede local para uma rede virtual Azure por meio de um gateway VPN do Azure.

>[AZURE.NOTE] Se você usar rota expressa com a opção de correspondência pública do Azure habilitada, uma rota estática deve ser criada. Isso deve rotear para o endereço de IP de VPN de NVA seu corporativo na Internet e não por meio de WAN a rota expressa. O NAT necessário na opção rota expressa do Azure público correspondência pode interromper a sessão VPN.

A figura a seguir mostra as duas bordas de rede nessa opção. Na primeira borda, a NVA e NSGs controle fluxos de tráfego para redes entre Azure e entre Azure e a Internet. A segunda borda é o gateway VPN do Azure, que é uma borda de rede completamente separada e isolada entre locais e do Azure.

Fluxos de tráfego devem ser considerados cuidadosamente, pois elas podem ser otimizadas ou degradados por esse padrão de design, dependendo do específicos do caso de uso.

Usando o ambiente integrado exemplo 1 e, em seguida, adicionar uma conexão de rede-to-site VPN híbridos, produz o design a seguir:

![Rede de perímetro com gateway conectado usando uma conexão de rota expressa][15]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede-to-site VPN híbrido para uma rede virtual Azure pode estender a rede local para Azure de maneira segura. Usando o gateway VPN Azure nativo, o tráfego é criptografados IPSec e roteia através da Internet. Além disso, usando o gateway Azure VPN pode fornecer uma opção de custo menor (sem licença adicional de custo como com terceiros NVAs). Isso é mais econômico no exemplo 1, onde não há NVA é usado. Para obter mais informações, consulte as instruções de compilação detalhadas (em breve). Essas instruções incluem:

- Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
- Como criar esse exemplo com um modelo do Gerenciador de recursos do Azure.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego flui através deste design.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemplo 6: Adicionar uma conexão de híbrido com rota expressa
[Voltar ao início rápido](#fast-start) | Construir obter instruções detalhadas disponíveis em breve

![Rede de perímetro com a rede do gateway híbrido conectada][16]

#### <a name="environment-description"></a>Descrição do ambiente
Rede híbrido usando uma conexão de correspondência particular rota expressa pode ser adicionada a qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Como mostrado na figura anterior, a rota expressa particular correspondência fornece uma conexão direta entre sua rede local e a rede virtual Azure. O tráfego de trânsito somente a rede de provedor de serviços e a rede do Microsoft Azure, nunca tocar na Internet.

>[AZURE.NOTE] Há algumas restrições ao usar UDR com rota expressa, devido a complexidade de roteamento dinâmico usado no Azure gateway virtual. Estas são da seguinte maneira:
>
>- UDR não deve ser aplicada à sub-rede gateway no qual o gateway de virtual Azure vinculado da rota expressa está conectado.
>- O gateway de virtual Azure vinculado da rota expressa não pode ser que o dispositivo NextHop para outro UDR acoplado sub-redes.
>
>
<br />

>[AZURE.TIP] Usando a rota expressa mantém o tráfego de rede corporativa de Internet para melhorar a segurança e significativamente maior desempenho. Ele também permite que os contratos de nível de serviço do seu provedor de rota expressa. O Gateway do Azure pode passar até 2 Gb/s com rota expressa, enquanto com VPNs to-site, a taxa de transferência máxima do Azure Gateway é 200 Mb/s.

Como visto no diagrama a seguir, com esta opção o ambiente agora tem duas bordas de rede. A NVA e NSG controle fluxos de tráfego para redes entre Azure e entre Azure e Internet, enquanto o gateway é uma borda de rede completamente separada e isolada entre locais e do Azure.

Fluxos de tráfego devem ser considerados cuidadosamente, pois elas podem ser otimizadas ou degradados por esse padrão de design, dependendo do específicos do caso de uso.

Usando o ambiente integrado exemplo 1 e em seguida, adicionar uma conexão de rede de híbrido rota expressa, produz o design a seguir:

![Rede de perímetro com gateway conectado usando uma conexão de rota expressa][17]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede privada de rota expressa Peering pode estender a rede local para Azure em uma latência segura, inferior, superior realizando maneira. Além disso, usar o Gateway nativo do Azure, como neste exemplo, fornece uma opção de custo menor (não adicional licenciamento como com terceiros NVAs). Para obter mais informações, consulte as instruções de compilação detalhadas (em breve). Essas instruções incluem:

- Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
- Como criar esse exemplo com um modelo do Gerenciador de recursos do Azure.
- Cenários de fluxo de tráfego detalhadas, mostrando como o tráfego flui através deste design.

## <a name="references"></a>Referências
### <a name="helpful-websites-and-documentation"></a>Documentação e sites úteis
- Azure no Access com o Azure Resource Manager:
- Acessando o Azure com o PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentação de rede virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentação do grupo de segurança de rede: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentação de roteamento definidas pelo usuário: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure gateways virtuais: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- [Https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) -to-Site VPNs:
- Rota expressa documentação (certifique-se de fazer check-out as seções de "Introdução" e "como"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Fluxograma de opções de segurança"
[1]: ./media/best-practices-network-security/compliancebadges.png "Identificações de conformidade do Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Recursos de segurança do Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "DMZ em uma rede corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitetura de segurança do Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "DMZ em uma rede Virtual Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rede de híbrido com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "Entrada DMZ com NSG"
[8]: ./media/best-practices-network-security/example2design.png "Entrada DMZ com NVA e NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Exibição lógica das regras de Firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ com NVA conectado rede híbrido"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA conectado usando uma VPN to-Site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rede lógica NVA perspectiva"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com Gateway Azure conectado Network-to-Site híbrido"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com Gateway Azure usando VPN to-Site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com Gateway Azure conectado rede rota expressa híbrido"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com Gateway Azure usando uma Conexão de rota expressa"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
