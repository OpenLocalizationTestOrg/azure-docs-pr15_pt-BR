<properties 
    pageTitle="Visão geral de arquitetura de rede de ambientes de serviço de aplicativo" 
    description="Visão geral da arquitetura de rede topologia ofApp ambientes de serviço." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="network-architecture-overview-of-app-service-environments"></a>Visão geral de arquitetura de rede de ambientes de serviço de aplicativo

## <a name="introduction"></a>Introdução ##
Ambientes de serviço de aplicativo sempre é criado em uma sub-rede de uma [rede virtual] [ virtualnetwork] -aplicativos em execução em um ambiente de serviço de aplicativo possam se comunicar com pontos de extremidade particulares localizados dentro da mesma topologia de rede virtual.  Como os clientes podem bloquear partes da sua infraestrutura de rede virtual, é importante entender os tipos de fluxos de comunicação de rede que ocorrem com um ambiente de serviço de aplicativo.

## <a name="general-network-flow"></a>Fluxo de rede geral ##
 
Quando um ambiente de serviço de aplicativo (ASE) usa um endereço IP virtual (VIP) público para aplicativos, todo o tráfego chega no que VIP público.  Isso inclui o tráfego HTTP e HTTPS para aplicativos, bem como outro tráfego para FTP, funcionalidade de depuração remota e operações de gerenciamento do Azure.  Para uma lista completa das portas específicas (obrigatórios e opcionais) que estão disponíveis no VIP público, consulte o artigo sobre como [controlar o tráfego de entrada] [ controllinginboundtraffic] para um ambiente de serviço de aplicativo. 

Ambientes de serviço de aplicativo também oferecem suporte a aplicativos em execução que são vinculados apenas para um endereço interno de rede virtual, também conhecido como um endereço de (balanceador de carga interna) ILB.  Em um ILB habilitado tráfego de ASE, HTTP e HTTPS para aplicativos, assim como chamadas de depuração remotas, chegam no endereço ILB.  Configurações de ILB ASE mais comuns, o tráfego FTP/FTPS também chegarão no endereço ILB.  No entanto operações de gerenciamento Azure ainda fluirá para portas 454/455 no VIP público de um ILB habilitado ASE.

O diagrama a seguir mostra uma visão geral dos fluxos de rede de entrada e saída de vários para um ambiente de serviço de aplicativo onde os aplicativos são vinculados a um endereço IP virtual público:

![Fluxos de rede geral][GeneralNetworkFlows]

Um ambiente de serviço de aplicativo pode se comunicar com uma variedade de pontos de extremidade do cliente particular.  Por exemplo, aplicativos executados no ambiente de serviço de aplicativo podem se conectar ao servidor (es) de banco de dados em execução no IaaS máquinas virtuais a mesma topologia de rede virtual.

>[AZURE.IMPORTANT] Observando o diagrama de rede, a "outros calcular recursos" são implantados em uma sub-rede diferente do ambiente de serviço de aplicativo. Implantação de recursos na mesma sub-rede do ASE bloqueará conectividade do ASE a esses recursos (exceto para roteamento de UE-ASE específico). Implante uma sub-rede diferente, em vez disso (em VNET a mesma). O ambiente de serviço de aplicativo será capaz de se conectar. Nenhuma configuração adicional é necessária.

Serviço de aplicativo ambientes também se comunicar com DB do Sql e armazenamento do Azure recursos necessários para gerenciamento e operação de um ambiente de serviço de aplicativo.  Alguns dos recursos Sql e armazenamento que um ambiente de serviço de aplicativo se comunica com estão localizados na mesma região como o ambiente de serviço de aplicativo, enquanto outras pessoas estão localizadas em regiões Azure remotos.  Como resultado, conectividade de saída com a Internet é sempre necessária para um ambiente de serviço de aplicativo funcionar corretamente. 

Como um ambiente de serviço de aplicativo for implantado em uma sub-rede, grupos de segurança de rede podem ser usados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um ambiente de serviço de aplicativo, consulte o seguinte [artigo][controllinginboundtraffic].

Para obter detalhes sobre como permitir saída conectividade com a Internet de um ambiente de serviço de aplicativo, consulte o seguinte artigo sobre como trabalhar com [Via expressa][ExpressRoute].  A mesma abordagem descrita no artigo se aplica ao trabalhar com conectividade to-Site e usando forçada túnel.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída ##
Quando um ambiente de serviço de aplicativo faz chamadas de saída, um endereço IP é sempre associado às chamadas de saída.  O endereço IP específico que é usado depende se o ponto de extremidade sendo chamado está localizado dentro da topologia de rede virtual ou fora a topologia de rede virtual.

Se o ponto de extremidade sendo chamado está **fora** da topologia de rede virtual, o endereço de saída (também conhecidos como o endereço NAT saído) que é usado é o VIP público do ambiente de serviço de aplicativo.  Este endereço pode ser encontrado na interface do usuário do portal para o ambiente de serviço de aplicativo em lâmina de propriedades.
 
![Endereço IP de saída][OutboundIPAddress]

Esse endereço também pode ser determinado para ASEs que possuem apenas um VIP público, criando um aplicativo no ambiente de serviço de aplicativo e, em seguida, executar um *nslookup* em endereço do aplicativo. O endereço IP resultante é tanto o VIP público, como endereço NAT da saída do ambiente de serviço de aplicativo.

Se o ponto de extremidade sendo chamado está **dentro** da topologia de rede virtual, o endereço de saída do aplicativo chamado será o endereço IP interno do recurso computação individuais executando o aplicativo.  No entanto não há um mapeamento persistente de endereços IP internos de rede virtual aos aplicativos.  Aplicativos podem se mover entre os recursos de computação diferentes e o pool de recursos em um ambiente de serviço de aplicativo podem alterar devido a escala operações de computação disponível.

No entanto, como um ambiente de serviço de aplicativo sempre está localizado em uma sub-rede, há garantia que o endereço IP interno de um recurso de computação executando um aplicativo sempre caberá dentro do intervalo CIDR da sub-rede.  Como resultado, quando ACLs refinadas ou grupos de segurança de rede são usados para acesso seguro a outros pontos de extremidade dentro da rede virtual, o intervalo de sub-rede contendo o ambiente de serviço de aplicativo precisa ter acesso.

O diagrama a seguir mostra esses conceitos em mais detalhes:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

- Como o VIP público do ambiente de serviço de aplicativo é 192.23.1.2, que é o endereço IP de saída usado ao fazer chamadas para pontos de extremidade de "Internet".
- O intervalo CIDR da sub-rede contendo para o ambiente de serviço de aplicativo é 10.0.1.0/26.  Outros pontos de extremidade na infraestrutura de rede virtual do mesmo verá chamadas de aplicativos como provenientes para outro dentro desse intervalo de endereço.

## <a name="calls-between-app-service-environments"></a>Chamadas entre ambientes de serviço de aplicativo ##
Um cenário mais complexo pode ocorrer se você implantar vários ambientes de serviço de aplicativo na mesma rede virtual e fazer chamadas de saída de um ambiente de serviço de aplicativo para outro ambiente de serviço de aplicativo.  Esses tipos de cross ambiente de serviço de aplicativo chamadas também serão tratadas como chamadas de "Internet".

O diagrama a seguir mostra um exemplo de uma arquitetura em camadas com aplicativos em um ambiente de serviço de aplicativo (por exemplo, "Porta frontal" web apps) chamar aplicativos em um segundo ambiente de serviço de aplicativo (por exemplo, interno back-end API apps não se destina a ser acessado da Internet). 

![Chamadas entre ambientes de serviço de aplicativo][CallsBetweenAppServiceEnvironments] 

No exemplo acima, o ambiente de serviço de aplicativo "ASE um" tem um endereço IP de saída de 192.23.1.2.  Se um aplicativo em execução no ambiente de serviço de aplicativo faz uma chamada de saída para um aplicativo em execução em um segundo ambiente de serviço de aplicativo ("ASE dois") localizada na mesma rede virtual, a chamada de saída será tratado como uma chamada de "Internet".  Como resultado o tráfego de rede que chegam na segunda ambiente de serviço de aplicativo mostrará como provenientes 192.23.1.2 (ou seja, não sub-rede intervalo de endereços do ambiente de serviço de aplicativo do primeiro).

Apesar de chamadas entre diferentes ambientes de serviço de aplicativo são tratadas como chamadas "Internet", quando os dois ambientes de serviço de aplicativo estão localizados na mesma região Azure o tráfego de rede permanecerão na rede Azure regional e não fluirá física na Internet pública.  Como resultado, você pode usar um grupo de segurança de rede da sub-rede o segundo ambiente de serviço de aplicativo para permitir apenas chamadas de entrada do primeiro aplicativo serviço ambiente (cujo endereço IP de saída é 192.23.1.2), garantindo comunicação segura entre os ambientes de serviço de aplicativo.

## <a name="additional-links-and-information"></a>Informações e Links adicionais ##
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para obter detalhes sobre portas usadas pelo aplicativo de serviço ambientes de entrada e usando grupos de segurança de rede para controlar o tráfego de entrada está disponível [aqui][controllinginboundtraffic].

Detalhes sobre o uso de usuário definidos rotas para conceder acesso de Internet de saída para ambientes de serviço de aplicativo está disponível neste [artigo][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

