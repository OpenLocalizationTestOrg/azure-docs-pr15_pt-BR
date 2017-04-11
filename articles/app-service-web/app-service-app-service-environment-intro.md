<properties 
    pageTitle="Introdução ao ambiente de serviço de aplicativo" 
    description="Saiba mais sobre o recurso de ambiente de serviço de aplicativo que fornece as unidades de escala de seguro, dedicado ingressaram VNet para execução de todos os seus aplicativos." 
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

# <a name="introduction-to-app-service-environment"></a>Introdução ao ambiente de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
Um ambiente de serviço de aplicativo é um [Premium] [ PremiumTier] opção de plano de serviço de aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado para execução com segurança de aplicativos de serviço de aplicativo do Azure em alta escala, incluindo [Web Apps]de serviço[WebApps], [Aplicativos Mobile][MobileApps]e [Aplicativos da API][APIApps].  

Serviço de aplicativo ambientes é ideal para cargas de trabalho de aplicativos que exigem:

- Escala muito alta
- Isolamento e acesso seguro à rede

Os clientes podem criar vários ambientes de serviço de aplicativo dentro de uma única região Azure, bem como em várias regiões Azure.  Isso torna ideal para dimensionar horizontalmente níveis de aplicativos de estado ao cargas de trabalho altas RPS ambientes de serviço de aplicativo.

Serviço de aplicativo ambientes é isolados executando apenas um único aplicativos do cliente e sempre é implantados em uma rede virtual.  Os clientes têm controle refinado sobre o tráfego de rede de ambos os aplicativos de entrada e saída, e aplicativos podem estabelecer conexões seguras alta velocidade redes virtual aos recursos corporativos local.

Todos os artigos e como-a sobre de ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para uma visão geral de como ambientes de serviço de aplicativo habilitar alta escala e proteger o acesso à rede, consulte o [Detalhamento de AzureCon] [ AzureConDeepDive] em ambientes de serviço de aplicativo!

Para uma análise aprofundada em dimensionamento horizontalmente usando vários ambientes de serviço de aplicativo consulte o artigo sobre como configurar um [espaço de aplicativo distribuído de localização geográfica][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança mostra o detalhamento de AzureCon foi configurada, consulte o artigo sobre como implementar um [em camadas de arquitetura de segurança](app-service-app-service-environment-layered-security.md) com ambientes de serviço de aplicativo.

Aplicativos em execução em ambientes de serviço de aplicativo podem ter seu acesso a determinado pelo upstream dispositivos como firewalls de aplicativo da web (WAF).  O artigo sobre como [Configurar um WAF para ambientes de serviço de aplicativo](app-service-app-service-environment-web-application-firewall.md) aborda este cenário. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicado ##
Todos os recursos de computação em um ambiente de serviço de aplicativo são dedicados exclusivamente a uma assinatura única e um ambiente de serviço de aplicativo pode ser configurado com até 50 (50) recursos de computação para uso exclusivo por um único aplicativo.

Um ambiente de serviço de aplicativo é composto de um pool de recursos de computação front-end, bem como um a três grupos de recursos de computação de trabalho. 

O pool de front-end contém recursos de computação responsável pelo encerramento de SSL como balanceamento de carga bem automática das solicitações de aplicativo em um ambiente de serviço de aplicativo. 

Cada pool de trabalho contém recursos de computação alocados para [Planos de serviço de aplicativo][AppServicePlan], que por sua vez contêm um ou mais aplicativos de serviço de aplicativo do Azure.  Como pode haver até três pools de trabalho diferentes em um ambiente de serviço de aplicativo, você tem a flexibilidade de escolha recursos de computação diferente para cada grupo de trabalho.  

Por exemplo, isso permite que você crie um pool de trabalho com menos poderosos recursos de computação para planos de serviço de aplicativo destinados para aplicativos de desenvolvimento ou teste.  Um pool de trabalhador segundo (ou até mesmo terceiro) poderia usar recursos de computação mais eficazes destinados para planos de serviço de aplicativo de execução de aplicativos de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, consulte [como configurar um ambiente de serviço de aplicativo][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre as disponíveis calcular tamanhos de recursos suportados em um ambiente de serviço de aplicativo, consulte os [Preços de serviço de aplicativo] [ AppServicePricing] página e revisar as opções disponíveis para ambientes de serviço de aplicativo no Premium preços camada.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##
Um ambiente de serviço de aplicativo pode ser criado em **qualquer** uma rede virtual do Gerenciador de recursos do Azure, **ou** uma implantação clássica de modelo de rede virtual ([obter mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Como um ambiente de serviço de aplicativo sempre existe em uma rede virtual e mais precisamente dentro de uma sub-rede de uma rede virtual, você pode aproveitar os recursos de segurança de redes virtuais para controlar ambas as comunicações de rede de entrada e saída.  

Um ambiente de serviço de aplicativo pode ser a Internet opostas com um endereço IP público, ou interno opostas com apenas um endereço de Balanceador de carga interno Azure (ILB).

Você pode usar [grupos de segurança de rede] [ NetworkSecurityGroups] para restringir a comunicação de rede de entrada para a sub-rede onde reside a um ambiente de serviço de aplicativo.  Isso permite que você execute aplicativos atrás upstream dispositivos e serviços como firewalls de aplicativo da web e provedores de SaaS de rede.

Aplicativos também frequentemente precisam acessar os recursos corporativos como bancos de dados internos e serviços da web.  Uma abordagem comum é fazer esses pontos de extremidade disponível somente para tráfego de rede interno fluindo dentro de uma rede virtual Azure.  Depois de um ambiente de serviço de aplicativo está associado à mesma rede virtual como os serviços internos, aplicativos executados no ambiente de podem acessá-los, incluindo pontos de extremidade acessíveis via [to-Site] [ SiteToSite] e [Rota expressa do Azure] [ ExpressRoute] conexões.

Para obter mais detalhes sobre como ambientes de serviço de aplicativo trabalhar com redes virtuais e local Consulte os artigos a seguir na [Arquitetura de rede][NetworkArchitectureOverview], [Controlando o tráfego de entrada][ControllingInboundTraffic]e [Uma conexão segura à back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o aplicativo de serviço ambientes, consulte [Como para criar um aplicativo do ambiente de serviço][HowToCreateAnAppServiceEnvironment]

Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

Para obter uma visão geral da arquitetura de rede do ambiente de serviço de aplicativo, consulte [Visão geral de arquitetura de rede] [ NetworkArchitectureOverview] artigo.

Para obter detalhes sobre como usar um ambiente de serviço de aplicativo com rota expressa, consulte o artigo a seguir em [ambientes de serviço de aplicativo e via expressa][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
