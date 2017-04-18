<properties
   pageTitle="Exemplo de DMZ – crie um DMZ para proteger redes com um Firewall, UDR e NSG | Microsoft Azure"
   description="Criar um DMZ com um Firewall, definidas pelo usuário roteamento (UDR) e grupos de segurança de rede (NSG)"
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
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3: criar um DMZ para proteger redes com um Firewall, UDR e NSG

[Retornar para a página práticas recomendada do limite de segurança][HOME]

Este exemplo criará um DMZ com um firewall, quatro servidores windows, roteamento de definidas pelo usuário, encaminhamento de IP e grupos de segurança de rede. Ele também guiará cada um dos comandos relevantes para fornecer uma compreensão mais profunda de cada etapa. Também há uma seção de cenário de tráfego para fornecer um profundamente detalhadas de como o tráfego passa para as camadas de proteção no DMZ. Finalmente, nas referências de seção é o código completo e instruções para criar esse ambiente para testar e experimentar vários cenários. 

![DMZ bidirecional com NVA, NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Três serviços em nuvem: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Uma rede Virtual "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
- Um dispositivo virtual de rede, neste exemplo, um firewall, conectada à sub-rede SecNet
- Windows Server que representa um aplicativo de servidor de web ("IIS01")
- Servidores de duas janelas que representam o aplicativo novamente encerrar servidores ("AppVM01", "AppVM02")
- Windows server que representa um servidor DNS ("DNS01")

Na seção de referências abaixo, há um script do PowerShell que criará maioria do ambiente descrito acima. Criando as VMs e redes virtuais, embora são feitas pelo script de exemplo, não são descritos detalhadamente neste documento.

Para criar o ambiente:

  1.    Salvar o arquivo xml de configuração de rede incluído na seção referências (atualizado com nomes, local e IP endereços para corresponder o cenário fornecido)
  2.    Atualizar as variáveis de usuário no script para corresponder ao ambiente que o script for executado contra (assinaturas, nomes de serviço, etc.)
  3.    Execute o script do PowerShell

**Observação**: A região representada no script PowerShell deve corresponder à região representada no arquivo xml de configuração da rede.

Depois que o script é executado com êxito as seguintes etapas de scripts POST podem ser tomadas:

1.  Configurar regras de firewall, isso é coberto na seção abaixo intitulada: Descrição da regra de Firewall.
2.  Opcionalmente, na seção referências são dois scripts para configurar o servidor web e servidor de aplicativo com um aplicativo web simples para permitir que o teste com essa configuração DMZ.

Depois que o script seja executado com êxito o firewall regras precisam ser concluídas, isso é coberto na seção intitulada: regras de Firewall.

## <a name="user-defined-routing-udr"></a>Roteamento (UDR) definidas pelo usuário
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

O VNETLocal é sempre a prefix(es) endereço definido do VNet para essa rede específico (ie ele será alterado de VNet para VNet dependendo de como cada VNet específica é definida). As rotas de sistema restantes são estáticas e padrão como acima.

Como prioridade, rotas são processadas por meio do método de maior prefixo correspondência LPM (), assim a rota mais específica na tabela seria aplicado a um determinado endereço de destino.

Portanto, tráfego (por exemplo, para o servidor de DNS01, 10.0.2.4) destinado para a rede local (10.0.0.0/16) deve ser roteado entre o VNet ao seu destino devido a rota de 10.0.0.0/16. Em outras palavras, para 10.0.2.4, a rota de 10.0.0.0/16 é a rota mais específica, mesmo que o 10.0.0.0/8/8 e 0.0.0.0/0 também poderiam se aplicam, mas desde que elas são menos específicas não afetam esse tráfego. Portanto, o tráfego para 10.0.2.4 seria tem um próximo nó de VNet o local e simplesmente rota para o destino.

Se o tráfego foi destinado 10.1.1.1 por exemplo, a rota de 10.0.0.0/16 não aplica, mas o 10.0.0.0/8 seria mais específica e isso seria o tráfego descartados ("preto holed") como o próximo salto é nulo. 

Se o destino não se aplicam a qualquer uma dos prefixos Null ou os prefixos VNETLocal, ele siga a menos específica rotear, 0.0.0.0/0 e ser circulados check-out na Internet como o próximo salto e, portanto, check-out de borda de internet do Azure.

Se houver dois prefixos idênticos na tabela de rotas, o seguinte é a ordem de preferência baseada no atributo "fonte" rotas:

1.  "VirtualAppliance" = uma rota de definido pelo usuário manualmente adicionado à tabela
2.  "VPNGateway" = uma rota de dinâmico (BGP quando usada com redes híbrido), adicionado por um protocolo de rede dinâmicos, essas rotas podem mudar ao longo do tempo conforme o protocolo dinâmico refletirá automaticamente as alterações na rede peered
3.  "Padrão" = as rotas de sistema, o local VNet e as entradas estáticas, conforme mostrado na tabela roteiro acima.

>[AZURE.NOTE] Agora você pode usar o roteamento definidas pelo usuário (UDR) com rota expressa e Gateways VPN para forçar o tráfego de cruz local de entrada e saído para ser roteados para um dispositivo de rede virtual (NVA).

#### <a name="creating-the-local-routes"></a>Criando as rotas locais

Neste exemplo, são necessárias duas tabelas de roteamento, um para as sub-redes front-end e back-end. Cada tabela é carregada com rotas estáticas apropriadas para a sub-rede determinada. Nesse exemplo, cada tabela tem três rotas:

1. Tráfego de sub-rede local com nenhum próximo salto definido para permitir o tráfego de sub-rede local para ignorar o firewall
2. Tráfego de rede virtual com um próximo nó definido como firewall, isso substitui a regra padrão que permite o tráfego de VNet local rotear diretamente
3. Todo restantes o tráfego (0/0) com um próximo nó definido como o firewall

Depois que as tabelas de roteamento são criadas, eles são vinculados a suas sub-redes. Para a sub-rede Frontend tabela de roteamento, uma vez criado e vinculado à sub-rede deve esta aparência:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Neste exemplo, os seguintes comandos são usados para criar o roteiro, adicionar uma rota definidas pelo usuário e, em seguida, vincular a tabela de rota a uma sub-rede (Observação; todos os itens abaixo começando com um cifrão (por exemplo: $BESubnet) são definidas pelo usuário variáveis do script na seção de referência deste documento):

1.  Primeiro a tabela de roteamento de base deve ser criada. Este trecho mostra a criação da tabela para a sub-rede de back-end. No script, uma tabela correspondente também é criada para a sub-rede Frontend.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Depois que a tabela de rota é criada, rotas de usuário específicas definida podem ser adicionadas. Neste capturados, todo o tráfego (0.0.0.0/0) será roteado através do aparelho virtual (uma variável, $VMIP [0], é usada para passar o endereço de IP atribuído quando o dispositivo virtual foi criado anteriormente no script). No script, uma regra correspondente também é criada na tabela Frontend.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. A entrada de rota acima substituirá a "0.0.0.0/0" rota padrão, mas a regra de 10.0.0.0/16 padrão ainda existente que permite que o tráfego dentro do VNet para rotear diretamente para o destino e não para o equipamento de rede Virtual. Para corrigir esse comportamento da regra de acompanhamento deve ser adicionado.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. Neste ponto, há uma opção para ser feita. Com as duas rotas acima encaminhe todo o tráfego para o firewall para avaliação, até mesmo tráfego dentro de uma única sub-rede. Isso pode ser desejável, mas para permitir o tráfego dentro de uma sub-rede para rotear localmente sem o envolvimento do firewall um terceiro, regra muito específica pode ser adicionada. Essa rota estados que qualquer endereço destine para sub-rede local pode simplesmente rotear lá diretamente (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Por fim, com a tabela de roteamento criada e preenchida com um rotas definidas pelo usuário, a tabela deve agora ser vinculada a uma sub-rede. No script, a tabela de rota de front-end também está vinculada à sub-rede Frontend. Aqui está o script de associação para a sub-rede de back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Encaminhamento de IP
Um recurso de companion para UDR, é o encaminhamento de IP. Esta é uma configuração em um dispositivo Virtual que permite que ela receba o tráfego não especificamente endereçado ao aparelho e encaminhar que o tráfego para seu destino final.

Como exemplo, se o tráfego de AppVM01 faz uma solicitação para o servidor de DNS01, UDR seria rotear isso ao firewall. Com o encaminhamento de IP habilitado, o tráfego para o destino de DNS01 (10.0.2.4) serão aceitas pelo dispositivo (10.0.0.4) e então encaminhado para seu destino final (10.0.2.4). Sem IP Forwarding habilitadas no Firewall, o tráfego não serão aceitos pelo aparelho mesmo que a tabela de rota tem o firewall como o salto seguinte. 

>[AZURE.IMPORTANT] É fundamental para se lembrar de habilitar o encaminhamento de IP em conjunto com o roteamento de definidas pelo usuário.

Configurando o encaminhamento de IP é um único comando e pode ser feito no momento da criação de máquina virtual. Para o fluxo de neste exemplo o trecho de código é até o final do script e agrupados com os comandos UDR:

1.  Chamar a instância de máquina virtual que é o seu dispositivo virtual, o firewall nesse caso e habilitar o encaminhamento de IP (Observação; qualquer item em vermelho, começando com um cifrão (por exemplo: $VMName[0]) é uma variável definidas pelo usuário do script na seção de referência deste documento. O zero colchetes, [0], representa a máquina virtual primeira na matriz de VMs, para o script de exemplo trabalhar sem modificação, a primeira máquina virtual (VM 0) deve ser o firewall):

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com uma única regra. Este grupo é associado somente às sub-redes front-end e back-end (não a SecNet). Forma declarativa a seguinte regra está sendo criada:

1.  Qualquer tráfego (todas as portas) da Internet para o VNet inteira (todas as sub-redes) é negado

Embora NSGs são usados neste exemplo, seu principal objetivo é como uma camada secundária de defesa contra configuração manual. Queremos bloquear todos os tráfego de entrada da internet tanto o Frontend ou sub-redes de back-end, o tráfego somente devem fluem sub-rede SecNet ao firewall (e, em seguida, se apropriado para o front-end ou back-end sub-redes). Além disso, com as regras UDR no lugar, qualquer tráfego que fez para as sub-redes Frontend ou back-end seria direcionado check-out para o firewall (graças ao UDR). O firewall poderia ser visto como um fluxo de assimétrico e seria solte o tráfego de saída. Portanto, há três camadas de segurança protegendo as sub-redes front-end e back-end; 1) nenhum ponto de extremidade aberto no FrontEnd001 e BackEnd001 serviços em nuvem, 2) NSGs negar o tráfego da Internet, o tráfego de assimétricos 3) o firewall soltar.

Um ponto interessante sobre o grupo de segurança de rede neste exemplo é que ela contém apenas uma regra, mostrada abaixo, que é negar o tráfego de internet para toda a rede virtual que incluiria a sub-rede de segurança. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

No entanto, como o NSG está ligado apenas para as sub-redes front-end e back-end, a regra não é processada no tráfego de entrada para a sub-rede de segurança. Como resultado, mesmo que a regra NSG não diz nenhum tráfego de Internet para qualquer endereço em VNet, porque o NSG nunca foi vinculado à sub-rede de segurança, o tráfego fluirá para a sub-rede de segurança.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Regras de firewall
No firewall, regras de encaminhamento precisa ser criado. Como o firewall está bloqueando ou encaminhamento tudo entrado, saído e UE-VNet tráfego muitas regras de firewall são necessários. Além disso, todo o tráfego atingirá o endereço IP público do serviço de segurança (em diferentes portas), para ser processada pelo firewall. Uma prática recomendada é criar um diagrama os fluxos de lógicos antes de configurar as sub-redes e regras de firewall para evitar reformular mais tarde. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:
 
![Exibição lógica das regras de Firewall][2]

>[AZURE.NOTE] Com base no dispositivo Virtual rede usado, as portas de gerenciamento variam. Neste exemplo, que um Firewall de NextGen Barracuda é referenciado que usa portas 22, 801 e 807. Consulte a documentação do fornecedor do dispositivo para localizar as portas exatas usadas para o gerenciamento do dispositivo sendo usado.

### <a name="logical-rule-description"></a>Descrição da regra lógico
No diagrama lógico acima, a sub-rede de segurança não é mostrada como o firewall é o único recurso nessa sub-rede, e este diagrama está mostrando as regras de firewall e como eles logicamente permitirem ou negar fluxos de tráfego e não o caminho roteado real. Além disso, as portas externas selecionadas para o tráfego RDP são mais altos intervalo de portas (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para fins de legibilidade fácil (por exemplo, o endereço de servidor local 10.0.1.4 está associado a porta externa 8014), contudo quaisquer portas superiores não conflitante podem ser usadas.

Neste exemplo, precisamos 7 tipos de regras, esses tipos de regra são descritos da seguinte maneira:

- Regras externas (para tráfego de entrada):
  1.    Regra de gerenciamento de firewall: Esta regra de redirecionamento de aplicativo permite o tráfego passar para as portas de gerenciamento do aparelho rede virtual.
  2.    Regras de RDP (para cada windows server): estes quatro regras (uma para cada servidor) permitirá gerenciamento dos servidores individuais via RDP. Isso também poderia ser agrupado em uma regra dependendo os recursos do dispositivo Virtual rede sendo usado.
  3.    Regras de tráfego de aplicativo: Há duas regras de tráfego de aplicativo, a primeira para o tráfego de web front-end e o segundo para o tráfego de back-end (por exemplo, o servidor web para nível de dados). A configuração dessas regras serão dependem de arquitetura de rede (onde seus servidores são colocadas) e fluxos de tráfego (direção na qual os fluxos de tráfego e quais portas são usadas).
      - A primeira regra permitirá que o tráfego de aplicativo real para acessar o servidor de aplicativo. Enquanto outras regras permitirem para segurança, gerenciamento, etc., regras de aplicativo são o que permite que usuários externos ou serviços para acessar os aplicativos. Neste exemplo, há um único servidor web na porta 80, portanto, uma regra de aplicativo de firewall único irá redirecioná-tráfego de entrada para o IP externo, o endereço IP interno do servidores de web. A sessão de tráfego redirecionado poderia ser NAT tinha para o servidor interno.
      - A segunda regra de tráfego de aplicativo é a regra de back-end para permitir que o servidor da Web para se comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer porta.
- Regras internas (para tráfego de VNet UE)
  4.    Saída a regra de Internet: esta regra permitirá tráfego de qualquer rede para passar para redes selecionadas. Essa regra é geralmente uma regra padrão já no firewall, mas em um estado desabilitado. Essa regra deve ser habilitada para esse exemplo.
  5.    Regra de DNS: Esta regra permite somente o tráfego DNS (porta 53) passar para o servidor DNS. Para esse ambiente que mais tráfego a Frontend no back-end é bloqueado, essa regra especificamente permitem que a DNS em qualquer sub-rede local.
  6.    Regra de sub-redes: esta regra é permitir que qualquer servidor na sub-rede back-end para se conectar a qualquer servidor na sub-rede front-end (mas não o inverso).
- Regra de falhas (para o tráfego que não atende a qualquer uma das opções acima):
  7.    Negar todo o tráfego regra: Isso sempre deve estar na regra final (em termos de prioridade) e como tal se um tráfego flui falhará corresponder a qualquer uma das regras a anterior, que ele será cancelado por esta regra. Esta é uma regra padrão e geralmente ativado, sem modificações geralmente são necessários.

>[AZURE.TIP] A segunda regra de tráfego de aplicativo, qualquer porta é permitida para fácil deste exemplo, em um cenário real a porta mais específica em intervalos de endereços devem ser usados para reduzir a superfície de ataque dessa regra.

<br />

>[AZURE.IMPORTANT] Depois que todas as regras acima são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme desejado. Neste exemplo, as regras são em ordem de prioridade. É fácil seja bloqueada fora do firewall devido a regras incorretamente ordenadas. No mínimo, certifique-se do que gerenciamento de firewall do próprio é sempre a regra de prioridade mais alta absoluto.

### <a name="rule-prerequisites"></a>Pré-requisitos de regra
Um pré-requisito para a máquina Virtual que executa o firewall são pontos de extremidade públicos. Para o firewall processar o tráfego de pontos de extremidade públicos apropriados devem estar abertos. Há três tipos de tráfego neste exemplo; Tráfego RDP 1) tráfego de gerenciamento de controle do firewall e regras de firewall, 2) para controlar os servidores windows e o tráfego de aplicativo 3). Estas são as três colunas de tipos de tráfego na parte superior metade de visualização lógica das regras de firewall acima.

>[AZURE.IMPORTANT] Uma chave takeway aqui é lembrar que **todo** o tráfego se torne através do firewall. Portanto para área de trabalho remota para o servidor IIS01, mesmo que ele esteja no serviço de nuvem de Front End e na sub-rede Front-End, para acessar este servidor nós será precisa RDP do firewall na porta 8014 e, em seguida, permitir o firewall rotear a solicitação RDP internamente para porta RDP IIS01. Botão de "Conectar" do portal Azure não funcionarão porque não há nenhum caminho RDP direto para IIS01 (quanto o portal pode ver). Isso significa que todas as conexões da internet serão para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx (referência diagrama acima para o mapeamento de porta externa e IP interno e porta).

Um ponto de extremidade pode ser aberto no momento da criação de máquina virtual ou poste compilação como é feita no script de exemplo e mostrado abaixo neste trecho de código (Observação; qualquer item que começa com um cifrão (por exemplo: $VMName[$i]) é uma variável definidas pelo usuário do script na seção de referência deste documento. O "$i" em colchetes, [$i], representa o número de matriz de uma máquina virtual específica em uma matriz de VMs):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Embora não claramente mostrado aqui devido ao uso de variáveis, mas pontos de extremidade são **somente** abriu o serviço de nuvem de segurança. Isso é para garantir que todo o tráfego é tratado (roteada, NAT tinha, ignorados) pelo firewall.

Um cliente de gerenciamento precisa estar instalado em um PC para gerenciar o firewall e crie as configurações necessárias. Consulte o fornecedor de documentação do seu firewall (ou outro NVA) sobre como gerenciar o dispositivo. O resto desta seção e a próxima seção, criação de regras de Firewall, descreverá a configuração do firewall em si, por meio do cliente de gerenciamento de fornecedores (ou seja, não o portal do Azure ou PowerShell).

Instruções para download do cliente e conectar o Barracuda usado neste exemplo podem ser encontradas aqui: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Uma vez conectado em firewall, mas antes de criar regras de firewall, há duas classes de objeto de pré-requisito que podem tornar a criação de regras mais fácil; Objetos de serviço e de rede.

Neste exemplo, três objetos de rede nomeadas devem ser definido (um para a sub-rede Frontend e a sub-rede de back-end, também um objeto de rede para o endereço IP do servidor DNS). Para criar uma rede nomeada; a partir do painel de administração de NG Barracuda do cliente, navegue até a guia de configuração, na seção configuração operacional, clique em conjunto de regras, clique em "Redes" no menu de objetos de Firewall e clique em novo no menu Editar redes. O objeto de rede agora pode ser criado, adicionando o nome e o prefixo:

![Criar um objeto de rede de front-end][3]
 
Isso criará uma rede nomeada para a sub-rede FrontEnd, um objeto semelhante deve ser criado para a sub-rede de back-end também. Agora as sub-redes podem ser referenciadas com mais facilidade pelo nome nas regras de firewall.

Para o objeto de servidor DNS:

![Criar um objeto de servidor DNS][4]

Esta referência de endereço IP única será utilizada em uma regra de DNS posteriormente no documento.

Os objetos de pré-requisito segundo são serviços. Esses representará as portas de conexão RDP para cada servidor. Como o objeto de serviço existente do RDP está ligado à porta RDP padrão, 3389, novos serviços podem ser criados para permitir o tráfego de portas externas (8014-8026). Novas portas também podem ser adicionadas ao serviço RDP existente, mas para facilitar a demonstração, uma regra individual para cada servidor pode ser criada. Para criar uma nova regra RDP para um servidor; a partir do painel de administração de NG Barracuda do cliente, navegue até a guia de configuração, na seção configuração operacional clique conjunto de regras, clique "Serviços" no menu de objetos de Firewall, role para baixo na lista de serviços e selecione o serviço "RDP". Clique com botão direito e selecione Copiar, e em seguida, clique com botão direito e selecione Colar. Agora há um objeto de serviço de RDP-Copy1 que podem ser editados. Clique com botão direito RDP-Copy1 e selecione Editar, o objeto de serviço Editar janela será exibida até conforme mostrado aqui:

![Cópia da regra RDP padrão][5]

Os valores podem ser editados para representar o serviço RDP para um servidor específico. Para AppVM01 a regra RDP padrão acima deve ser modificada para refletir um novo nome de serviço, descrição e porta RDP externa usados no diagrama Figura 8 (Observação: as portas são alteradas do padrão de RDP de 3389 à porta externa sendo usada para este servidor específico, no caso de AppVM01 porta externa é 8025) o serviço modificado é mostrado abaixo :

![Regra de AppVM01][6]
 
Esse processo deve ser repetido para criar serviços de RDP para os servidores restantes; AppVM02, DNS01 e IIS01. A criação desses serviços fará a criação de regra mais simples e mais óbvias na próxima seção.

>[AZURE.NOTE] Um serviço RDP para o Firewall não é necessário por duas razões; 1) primeiro o firewall máquina virtual é uma imagem de baseado em Linux para SSH seria usado na porta 22 para gerenciamento de máquina virtual em vez de RDP e 2) porta 22 e duas outras portas de gerenciamento são permitidas na primeira regra de gerenciamento descrita a seguir para permitir para conectividade de gerenciamento.

### <a name="firewall-rules-creation"></a>Criação de regras de firewall
Há três tipos de regras de firewall usados neste exemplo, todos eles têm ícones distintas:

A regra de aplicativo redirecionar: ![Ícone redirecionar do aplicativo][7]

A regra de destino NAT: ![Ícone de NAT de destino][8]

A regra de fase: ![Passar ícone][9]

Obter mais informações sobre essas regras podem ser encontradas no site Barracuda.

Para criar as seguintes regras (ou verificar as regras existentes de padrão), começando no painel de cliente Barracuda NG Admin, navegue até a guia de configuração, na configuração operacionais seção clique em conjunto de regras. Uma grade chamada, "Principais regras" mostrará as regras existentes de ativas e desativadas esse firewall. No canto superior direito da grade desta é uma pequena verde "+" botão, clique nesta opção para criar uma nova regra (Observação: o firewall pode ser "bloqueado" para alterações, se você vir um botão marcado "Bloquear" e não for possível criar ou editar regras, clique neste botão para "desbloquear" o conjunto de regras e permitir edição). Se você quiser editar uma regra existente, selecione essa regra, clique com botão direito e selecione Editar regra.

Depois que as regras são criadas e/ou modificadas, deve ser enviados ao firewall e, em seguida, ativados, se isso não é feito as alterações de regra não terá efeito. O processo de ativação e de envio é descrito abaixo as descrições das regras de detalhes.

As especificações de cada regra que é necessário para concluir este exemplo são descritas da seguinte maneira:

- **Regra de gerenciamento de firewall**: este aplicativo redirecionar regra permite o tráfego passar para as portas de gerenciamento de dispositivo virtual rede, neste exemplo Barracuda NextGen Firewall. As portas de gerenciamento são 801, 807 e opcionalmente 22. As portas internas e externas são os mesmos (ou seja, sem conversão de porta). Esta regra, acesso ao gerenciamento de configuração, é uma regra padrão e ativado por padrão (Barracuda NextGen Firewall versão 6.1).

    ![Regra de gerenciamento de firewall][10]

>[AZURE.TIP] O espaço de endereço de origem nesta regra é qualquer, se os intervalos de endereços IP de gerenciamento são conhecidos, reduzir este escopo seria também reduzir a superfície de ataque às portas de gerenciamento.

- **Regras de RDP**: regras de NAT de destino estes permitirá gerenciamento dos servidores individuais via RDP.
Há quatro campos crítica necessários para criar esta regra:
  1.    Fonte – para permitir que o RDP de qualquer lugar, a referência "Qualquer" é usada no campo origem.
  2.    Use o serviço – o objeto de serviço apropriado criado anteriormente, neste caso "AppVM01 RDP", portas externas redirecionar o endereço IP local de servidores e porta 3386 (a porta padrão RDP). Essa regra específica é para acessar RDP AppVM01.
  3.    Destino – deve ser a *porta local no firewall*, "IP Local DCHP 1" ou eth0 se usando endereços IP estático. O número ordinal (eth0, eth1 etc.) podem ser diferente se o equipamento de rede tem várias interfaces locais. Esta é a porta o firewall está enviando de (pode ser a mesma que a porta de recebimento), o destino roteado real está no campo de lista de destino.
  4.    Redirecionamento – esta seção informa ao aparelho virtual onde basicamente redirecionar esse tráfego. O redirecionamento mais simples é colocar o IP e a porta (opcional) no campo de lista de destino. Se nenhuma porta for usada a porta de destino na solicitação de entrada será usado (ie sem conversão), se houver uma porta designada a porta também será NAT seria juntamente com o IP de endereços.

    ![Regra de RDP do firewall][11]

    Um total de quatro regras RDP precisará ser criada: 

  	|   Nome da regra    | Servidor  |   Serviço   |  Lista de destino  |
  	|----------------|---------|-------------|---------------|
  	| RDP para IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP para DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP para AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP para AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Limitar o escopo dos campos de origem e de serviço irá reduzir a superfície de ataque. O escopo mais limitado que permitirá funcionalidade deve ser usado.

- **Regras de tráfego de aplicativo**: há duas regras de tráfego de aplicativo, a primeira para o tráfego de web front-end e o segundo para o tráfego de back-end (por exemplo, o servidor web para nível de dados). Essas regras serão dependem de arquitetura de rede (onde seus servidores são colocadas) e fluxos de tráfego (direção na qual os fluxos de tráfego e quais portas são usadas).

    Primeiro discutido é a regra de front-end para tráfego da web:

    ![Regra de Web de firewall][12]
 
    Essa regra de destino NAT permite que o tráfego de aplicativo real para acessar o servidor de aplicativo. Enquanto outras regras permitirem para segurança, gerenciamento, etc., regras de aplicativo são o que permite que usuários externos ou serviços para acessar os aplicativos. Neste exemplo, há um único servidor web na porta 80, portanto, a regra de aplicativo de firewall único irá redirecioná-tráfego de entrada para o IP externo, o endereço IP interno do servidores de web.

    **Observação**: que não há nenhuma porta atribuída no campo de lista de destino, portanto, a porta de entrada 80 (ou 443 para o serviço selecionado) será usada no redirecionamento do servidor web. Se o servidor web é listening em uma porta diferente, por exemplo porta 8080, o campo da lista de destino pode ser atualizado para 10.0.1.4:8080 para permitir o redirecionamento de porta também.

    A próxima regra de tráfego de aplicativo é a regra de back-end para permitir que o servidor da Web para se comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer serviço:

    ![Regra de AppVM01 de firewall][13]

    Essa regra de fase permite que qualquer servidor IIS na sub-rede Frontend alcançar a AppVM01 (endereço IP 10.0.2.5) em qualquer porta, usando qualquer protocolo para acessar os dados necessitados para o aplicativo da web.

    Esta captura de tela um "\<explícitas destino\>" é usado no campo de destino para indicar 10.0.2.5 como destino. Isso pode ser uma chamada explícita conforme mostrado ou um objeto de rede (como foi feito nos pré-requisitos para o servidor DNS). Isso é para cima até o administrador do firewall qual método será usado. Para adicionar 10.0.2.5 como um Explict Desitnation, clique duas vezes na primeira linha em branco em \<explícitas destino\> e digite o endereço na janela pop-up.

    Com essa regra passar, sem NAT é necessária porque este é o tráfego interno, para que o método de Conexão pode ser definido como "Não SNAT".

    **Observação**: rede de origem a essa regra é qualquer recurso na sub-rede FrontEnd, se só haverá uma, ou um número específico conhecido de servidores web, um recurso de objeto de rede pode ser criado para ser mais específico aos endereços IP exatos em vez da sub-rede Frontend inteira.

>[AZURE.TIP] Esta regra usa o serviço "Qualquer" para facilitar a configuração e o uso do aplicativo de amostra, também poderá ICMPv4 (ping) em uma única regra. No entanto, isso não é uma prática recomendada. As portas e protocolos ("serviços") devem ser restringidos para o mínimo possível que permite a operação de aplicativo reduzir a superfície de ataque por esse limite.

<br />

>[AZURE.TIP] Embora esta regra mostra uma referência de explícita destino sendo usada, uma abordagem consistente deve ser usada em toda a configuração de firewall. É recomendável que o objeto de rede nomeado ser usado em todo para suporte e mais fácil legibilidade. O destino-explícita só é usado aqui para mostrar um método alternativo de referência e geralmente não é recomendado (especialmente para configurações complexas).

- **Saída a regra de Internet**: passar esta regra permitirá tráfego de qualquer rede de origem para passar para redes de destino selecionadas. Essa regra é uma regra de padrão geralmente já no firewall Barracuda NextGen, mas está em um estado desabilitado. Clicando nessa regra pode acessar o comando ativar regra. A regra mostrada aqui foi modificada para adicionar as duas sub-redes locais que foram criadas como referências na seção pré-requisito deste documento para o atributo de origem dessa regra.

    ![Regra de saída do firewall][14]

- **Regra de DNS**: passar esta regra permite apenas o tráfego DNS (porta 53) passar para o servidor DNS. Para esse ambiente que mais tráfego a FrontEnd no back-end é bloqueado, essa regra especificamente permite DNS.

    ![Regra de DNS de firewall][15]

    **Observação**: nesta tela captura o método de Conexão está incluída. Como essa regra é para o IP interno para tráfego de endereço IP interno, sem NATing é necessária, isso o método de Conexão é definido como "Não SNAT" para esta regra de fase.

- **Regra de sub-redes**: passar esta regra é uma regra de padrão que foi ativada e modificada para permitir que qualquer servidor na sub-rede back-end para se conectar a qualquer servidor na sub-rede front-end. Essa regra é tráfego tudo interno para que o método de Conexão pode ser definido como sem SNAT.

    ![Regra de VNet dentro do firewall][16]

    **Observação**: A caixa de seleção bidirecional não está marcada (nem é feito check-in a maioria das regras), isso é significativo para esta regra em que faz isso regra "única direção", uma conexão pode ser iniciado da sub-rede back-end para a rede de front-end, mas não o contrário. Se essa caixa de seleção foi marcada, essa regra permite o tráfego bidirecional, que não é desejado do nosso diagrama lógico.

- **Negar todas as regras de tráfego**: isso sempre deve estar na regra final (em termos de prioridade) e como tal se um tráfego flui falhar para coincidir com nenhum dos anterior regras a ele será cancelado por esta regra. Esta é uma regra padrão e geralmente ativado, sem modificações geralmente são necessários. 

    ![Firewall negar regra][17]

>[AZURE.IMPORTANT] Depois que todas as regras acima são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme desejado. Neste exemplo, as regras são na ordem em que eles devem aparecer na grade de principal de regras no cliente de gerenciamento de Barracuda de encaminhamento.

## <a name="rule-activation"></a>Ativação de regra
Com o conjunto de regras que modificou a especificação do diagrama lógica, o conjunto de regras deve ser carregado para o firewall e, em seguida, ativado.

![Ativação de regra de firewall][18]
 
No canto superior direito do cliente management são um cluster dos botões. O botão "Enviar alterações" para enviar as regras modificadas ao firewall, clique no botão "Ativar".
 
Com a ativação do conjunto de regras de firewall esta compilação do ambiente de exemplo está concluída.

## <a name="traffic-scenarios"></a>Cenários de tráfego
>[AZURE.IMPORTANT] Uma chave takeway é lembrar que **todo** o tráfego se torne através do firewall. Portanto para área de trabalho remota para o servidor IIS01, mesmo que ele esteja no serviço de nuvem de Front End e na sub-rede Front-End, para acessar este servidor nós será precisa RDP do firewall na porta 8014 e, em seguida, permitir o firewall rotear a solicitação RDP internamente para porta RDP IIS01. Botão de "Conectar" do portal Azure não funcionarão porque não há nenhum caminho RDP direto para IIS01 (quanto o portal pode ver). Isso significa que todas as conexões da internet serão para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx.

Para esses cenários, as seguintes regras de firewall devem estar no lugar:

1.  Gerenciamento de firewall
2.  RDP para IIS01
3.  RDP para DNS01
4.  RDP para AppVM01
5.  RDP para AppVM02
6.  Tráfego de aplicativo na Web
7.  Tráfego de aplicativo para AppVM01
8.  Saída para a Internet
9.  Frontend para DNS01
10. Tráfego de dentro da sub-rede (back-end para front-end somente)
11. Negar tudo

O conjunto de regras de firewall reais provavelmente terá muitas outras regras além esses, as regras em qualquer firewall determinado também terá números de prioridade diferente que os listados aqui. Esta lista e números associados são fornecer relevância entre apenas essas 11 regras e a prioridade relativa entre eles. Em outras palavras; no firewall real, o "RDP para IIS01" pode ser regra número 5, mas desde que é abaixo da regra de "Gerenciamento de Firewall" e acima da regra de "RDP para DNS01" seria alinhar com a intenção dessa lista. A lista também ajudará aos cenários permitindo Resumir; abaixo Por exemplo, "FW regra 9 (DNS)". Também encontram, as quatro regras RDP será coletivamente chamado, "as regras RDP" quando o cenário de tráfego está relacionado ao RDP.

Também Lembre-se de que os grupos de segurança de rede são in-loco para tráfego da internet das sub-redes front-end e back-end.

#### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para o servidor Web
1.  Página HTTP de solicitações de usuário de Internet do SecSvc001.CloudApp.Net (Internet opostas serviço em nuvem)
2.  Tráfego de passagens de serviço de nuvem por meio de ponto de extremidade aberto na porta 80 a interface de firewall 10.0.0.4:80
3.  Sem NSG atribuído a sub-rede de segurança, regras NSG de sistema assim permitirem o tráfego ao firewall
4.  Tráfego visitas endereço IP interno do firewall (10.0.1.4)
5.  Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    Regras FW (RDP regras) de 2 a 5 não aplicar, mover para a próxima regra
  3.    FW regra 6 (aplicativo: Web) aplicar, tráfego é permitido, NATs de firewall para 10.0.1.4 (IIS01)
6.  A sub-rede Frontend começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplica (esse tráfego foi NAT seria pelo firewall, portanto, o endereço de origem agora é o firewall que esteja na sub-rede segurança e visto por sub-rede Frontend NSG tráfego de "local" e, portanto, é permitido), mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
7.  IIS01 está aguardando tráfego da web, recebe essa solicitação e inicia o processamento da solicitação
8.  IIS01 tenta inicia uma sessão FTP para AppVM01 na sub-rede de back-end
9.  A rota UDR na sub-rede Frontend torna o firewall o próximo salto
10. Não há regras de saída em sub-rede Frontend, o tráfego é permitido
11. Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    FW regra 2 a 5 (RDP regras) não se aplicam, mover para a próxima regra
  3.    FW regra 6 (aplicativo: Web) não se aplicam, mover para a próxima regra
  4.    FW regra 7 (aplicativo: back-end) aplicar, o tráfego é permitido, firewall encaminha tráfego para 10.0.2.5 (AppVM01)
12. A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
13.  AppVM01 recebe a solicitação e inicia a sessão e responde
14. A rota UDR na sub-rede de back-end torna o firewall o próximo salto
15. Como não há nenhuma regra NSG saída na sub-rede back-end que a resposta é permitida
16. Porque isso está retornando tráfego em uma sessão estabelecida o firewall passa a resposta ao servidor da web (IIS01)
17. Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
18. O servidor IIS recebe a resposta, conclui a transação com AppVM01 e conclui criando a resposta HTTP, esta resposta HTTP é enviada ao solicitante
19. Como não há nenhuma regra NSG saída na sub-rede Frontend que a resposta é permitida
20. A resposta HTTP atinge o firewall e porque esta é a resposta para uma sessão de NAT estabelecida seja aceito pelo firewall
21. O firewall redireciona a resposta ao usuário da Internet
22. Uma vez que existem em saída não NSG regras ou saltos UDR na sub-rede Frontend a resposta é permitida e o usuário da Internet recebe a página da web solicitada.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitido) Internet RDP para back-end
1.  Administrador do servidor em internet solicita sessão RDP AppVM01 via SecSvc001.CloudApp.Net:8025, onde 8025 é o número de porta de usuário atribuído para a regra de firewall "RDP para AppVM01"
2.  O serviço de nuvem passa o tráfego através do ponto de extremidade aberto na porta 8025 a interface de firewall no 10.0.0.4:8025
3.  Sem NSG atribuído a sub-rede de segurança, regras NSG de sistema assim permitirem o tráfego ao firewall
4.  Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    FW regra 2 (RDP IIS) não se aplicam, mover para a próxima regra
  3.    FW regra 3 (RDP DNS01) não se aplicam, mover para a próxima regra
  4.    FW regra 4 (RDP AppVM01) aplicar, o tráfego é permitido, NATs de firewall para 10.0.2.5:3386 (porta RDP no AppVM01)
5.  A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplica (esse tráfego foi NAT seria pelo firewall, portanto, o endereço de origem agora é o firewall que esteja na sub-rede segurança e visto por sub-rede back-end NSG tráfego de "local" e, portanto, é permitido), mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
6.  AppVM01 está aguardando tráfego RDP e responde
7.  Com regras de NSG sem saída, aplicam regras padrão e o tráfego de retorno é permitido
8.  UDR rotear o tráfego de saída para o firewall como o salto seguinte
9.  Porque isso está retornando tráfego em uma sessão estabelecida o firewall passa a resposta ao usuário da internet
10. Sessão RDP está habilitado
11. AppVM01 solicita a senha do nome de usuário

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do servidor de Web no servidor DNS
1.  Servidor, IIS01, necessidades de feed de um dados em www.data.gov, mas precisa resolver o endereço da Web.
2.  A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede back-end) como o servidor DNS primário, IIS01 envia a solicitação DNS para DNS01
3.  UDR rotear o tráfego de saída para o firewall como o salto seguinte
4.  Nenhuma regra NSG saída é vinculada à sub-rede Frontend, o tráfego é permitido
5.  Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    FW regra 2 a 5 (RDP regras) não se aplicam, mover para a próxima regra
  3.    FW regras 6 e 7 (regras de aplicativo) não se aplicam, mover para a próxima regra
  4.    FW regra 8 (à Internet) não se aplicam, mover para a próxima regra
  5.    Aplicar regra de FW 9 (DNS), o tráfego é permitido, firewall encaminha tráfego para 10.0.2.4 (DNS01)
6.  A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
7.  Servidor DNS recebe a solicitação
8.  Servidor DNS não tem o endereço de cache e solicita um servidor DNS raiz na internet
9.  UDR rotear o tráfego de saída para o firewall como o salto seguinte
10. Nenhum NSG as regras de saída em sub-rede de back-end, o tráfego é permitido
11. Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    FW regra 2 a 5 (RDP regras) não se aplicam, mover para a próxima regra
  3.    FW regras 6 e 7 (regras de aplicativo) não se aplicam, mover para a próxima regra
  4.     Aplicar FW regra 8 (a Internet), o tráfego é permitido, sessão é SNAT check-out ao servidor DNS de raiz na Internet
12. Servidor DNS de Internet responde, desde que esta sessão foi iniciada a partir do firewall, a resposta é aceita pelo firewall
13. Como isso é uma sessão estabelecida, o firewall encaminha a resposta para o servidor Iniciando, DNS01
14. A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
15. O servidor DNS recebe armazenará a resposta e, em seguida, responde à solicitação inicial para IIS01
16. A rota UDR na sub-rede de back-end torna o firewall o próximo salto
17. Nenhuma regra NSG saída existe na sub-rede back-end, o tráfego é permitido
18. Esta é uma sessão estabelecida no firewall, a resposta é encaminhada pelo firewall volta para o servidor do IIS
19. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não há nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede Frontend, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema padrão permitir o tráfego entre sub-redes seria permitir este tráfego para que o tráfego é permitido
20. IIS01 recebe a resposta do DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitido) Servidor de back-end ao servidor front-end
1.  Um administrador conectado à AppVM02 via RDP solicita um arquivo diretamente do servidor IIS01 via Explorador de arquivos do windows
2.  A rota UDR na sub-rede de back-end torna o firewall o próximo salto
3.  Como não há nenhuma regra NSG saída na sub-rede back-end que a resposta é permitida
4.  Firewall inicia o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    FW regra 2 a 5 (RDP regras) não se aplicam, mover para a próxima regra
  3.    FW regras 6 e 7 (regras de aplicativo) não se aplicam, mover para a próxima regra
  4.    FW regra 8 (à Internet) não se aplicam, mover para a próxima regra
  5.    FW regra 9 (DNS) não se aplicam, mover para a próxima regra
  6.    FW regra 10 (UE-sub-rede) aplique, o tráfego é permitido, firewall passa o tráfego para 10.0.1.4 (IIS01)
5.  Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
6.  Considerando que autorização e autenticação adequada, IIS01 aceita a solicitação e responde
7.  A rota UDR na sub-rede Frontend torna o firewall o próximo salto
8.  Como não há nenhuma regra NSG saída na sub-rede Frontend que a resposta é permitida
9.  Como isso é uma sessão existente no firewall esta resposta é permitida e o firewall retorna a resposta ao AppVM02
10. Sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (bloco Internet) não se aplicam, mover para a próxima regra
  2.    Regras de NSG padrão permitir o tráfego de sub-redes, o tráfego é permitido, interrompa o processamento de regras NSG
11. AppVM02 recebe a resposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta para o servidor Web
1.  Usuário de Internet tenta acessar o servidor da web, IIS01, por meio do serviço de FrontEnd001.CloudApp.Net
2.  Como não há nenhum ponto de extremidade aberto para tráfego HTTP, isso não seria passam pelo serviço de nuvem e não alcançar o servidor
3.  Se os pontos de extremidade foram abertos por algum motivo, o NSG (bloco Internet) na sub-rede Frontend bloquear esse tráfego
4.  Por fim, a rota UDR de sub-rede Frontend envia qualquer tráfego de saída de IIS01 ao firewall como o salto seguinte e o firewall seria visto como tráfego assimétrico e solte a resposta de saída, portanto, há pelo menos três camadas independentes de defesa entre a internet e IIS01 por meio de seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-internet-to-backend-server"></a>(Negado) Internet para o servidor back-end
1.  Usuário de Internet tentar acessar um arquivo em AppVM01 através do serviço de BackEnd001.CloudApp.Net
2.  Como não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, isso não seria passar o serviço de nuvem e não alcançar o servidor
3.  Se os pontos de extremidade foram abertos por algum motivo, o NSG (bloco Internet) bloquear esse tráfego
4.  Por fim, a rota UDR envia qualquer tráfego de saída de AppVM01 ao firewall como o salto seguinte e o firewall seria visto como tráfego assimétrico e solte a resposta de saída, portanto, há pelo menos três camadas independentes de defesa entre a internet e AppVM01 por meio de seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negado) Servidor de frontend ao servidor back-end
1.  Suponha IIS01 foi comprometida e está executando o código mal-intencionado tentando examinar os servidores de sub-rede de back-end.
2.  A rota UDR de sub-rede Frontend seria enviar qualquer tráfego de saída do IIS01 ao firewall como o salto seguinte. Isso não é algo que possa ser alterado pela máquina virtual comprometida.
3.  O firewall faria processar o tráfego, se a solicitação foi para AppVM01 ou para o servidor DNS para pesquisas DNS que tráfego potencialmente poderia ser permitido pelo firewall (devido a FW regras 7 e 9). Todo o tráfego seria bloqueado pela FW regra 11 (Negar todos).
4.  Se a detecção de ameaças avançada foi habilitado no firewall (que não é coberto neste documento, consulte a documentação do fornecedor de seu aparelho de rede específica avançados de ameaças) par tráfego que deve ser permitido pelas regras de encaminhamento básico discutidas neste documento poderia ser impedido se o tráfego contido assinaturas conhecidas ou padrões que sinalizar uma regra de ameaças avançadas.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS da Internet no servidor DNS
1.  Usuário de Internet tentar pesquisar um registro DNS interno na DNS01 por meio de serviço de BackEnd001.CloudApp.Net 
2.  Como não há nenhum ponto de extremidade aberto para que o tráfego DNS, isso não seria passam pelo serviço de nuvem e não alcançar o servidor
3.  Se os pontos de extremidade foram abertos por algum motivo, a regra NSG (bloco Internet) na sub-rede Frontend bloquear esse tráfego
4.  Por fim, a rota UDR de sub-rede de back-end envia qualquer tráfego de saída de DNS01 ao firewall como o salto seguinte e o firewall seria visto como tráfego assimétrico e solte a resposta de saída, portanto, há pelo menos três camadas independentes de defesa entre a internet e DNS01 por meio de seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para acessar o SQL pelo Firewall
1.  Usuário de Internet solicita dados SQL do SecSvc001.CloudApp.Net (Internet opostas serviço em nuvem)
2.  Como não há nenhum ponto de extremidade aberto para SQL, isso não seria passar o serviço de nuvem e não atinja o firewall
3.  Se pontos de extremidade do SQL foram abertos por algum motivo, o firewall faria começar o processamento de regras:
  1.    FW regra 1 (FW gerenciamento) não se aplicam, mover para a próxima regra
  2.    Regras FW (RDP regras) de 2 a 5 não aplicar, mover para a próxima regra
  3.    FW regra 6 e 7 (regras de aplicativo) não se aplicam, mover para a próxima regra
  4.    FW regra 8 (à Internet) não se aplicam, mover para a próxima regra
  5.    FW regra 9 (DNS) não se aplicam, mover para a próxima regra
  6.    FW regra 10 (UE-sub-rede) não se aplicam, mover para a próxima regra
  7.    FW regra 11 (Negar todos) aplicar, o tráfego é o processamento de regra bloqueadas, interromper


## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Salve o Script completo em um arquivo de script do PowerShell. Salve a configuração de rede em um arquivo denominado "NetworkConf2.xml".
Modifique as variáveis definidas pelo usuário conforme necessário. Execute o script e, em seguida, siga as instruções de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script completo
Este script irá, com base nas variáveis definidas pelo usuário:

1.  Conectar a uma assinatura do Azure
2.  Criar uma nova conta de armazenamento
3.  Criar um novo VNet e três sub-redes conforme definido no arquivo de configuração de rede
4.  Criar cinco máquinas virtuais; 1 firewall e 4 windows server VMs
5.  Configure UDR incluindo:
  1.    Criar duas novas tabelas de roteiro
  2.    Adicionar rotas a tabelas
  3.    Vincular tabelas para sub-redes apropriados
6.  Habilitar o encaminhamento de IP na NVA
7.  Configure NSG incluindo:
  1.    Criando um NSG
  2.    Adicionando uma regra
  3.    Vinculando a NSG para as sub-redes apropriadas

Este script do PowerShell deve ser executado localmente na que Internet conectado PC ou servidor.

>[AZURE.IMPORTANT] Quando esse script é executado, pode haver avisos ou outras mensagens informativas que pop no PowerShell. Apenas as mensagens de erro em vermelho são causa do problema.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Arquivo de configuração de rede
Salvar o arquivo xml com local atualizado e adicionar o link para esse arquivo para a variável de $NetworkConfigFile no script acima.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemplos de Scripts de aplicativo
Se você desejar instalar um aplicativo de amostra para esse e outros exemplos de DMZ, um foi fornecido no seguinte link: [Script do aplicativo de amostra][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Exibição lógica das regras de Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia da regra RDP padrão"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone de redirecionamento de aplicativo"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ícone de passagem"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de gerenciamento de firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra de RDP do firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra de Web de firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 de firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de saída do firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de DNS de firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra de VNet dentro do firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Firewall negar regra"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
