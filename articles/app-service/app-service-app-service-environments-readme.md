<properties 
    pageTitle="Ambiente de serviço de aplicativo | Microsoft Azure" 
    description="O que é um ambiente de serviço de aplicativo do Azure? Uma introdução ao ambiente de serviço de aplicativo." 
    keywords="ambiente de serviço de aplicativo do Azure, rede virtual, proteger rede"
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

# <a name="app-service-environment-documentation"></a>Documentação do ambiente de serviço de aplicativo

Um ambiente de serviço de aplicativo é um [Premium] [ PremiumTier] opção de plano de serviço de aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado para execução com segurança de aplicativos de serviço de aplicativo do Azure em alta escala, incluindo [Aplicativos Web]do serviço[WebApps], [Aplicativos Mobile][MobileApps]e [Aplicativos da API][APIApps].  

Serviço de aplicativo ambientes é ideal para cargas de trabalho de aplicativos que exigem:

- Escala muito alta
- Isolamento e acesso seguro à rede

Os clientes podem criar vários ambientes de serviço de aplicativo dentro de uma única região Azure, bem como em várias regiões Azure.  Isso torna ideal para dimensionar horizontalmente níveis de aplicativos de estado ao cargas de trabalho altas RPS ambientes de serviço de aplicativo.

Serviço de aplicativo ambientes é isolados executando apenas um único aplicativos do cliente e sempre é implantados em uma rede virtual.  Os clientes têm controle refinado sobre as duas tráfego de rede de entrada e saída aplicativo usando [grupos de segurança de rede][NetworkSecurityGroups].  Aplicativos também podem estabelecer conexões seguras alta velocidade redes virtual aos recursos corporativos local.

Aplicativos frequentemente precisam acessar os recursos corporativos como bancos de dados internos e serviços da web.  Aplicativos em execução no aplicativo de serviço ambientes podem acessar os recursos acessíveis via [to-Site] [ SiteToSite] VPN e [Rota expressa do Azure] [ ExpressRoute] conexões.

* [O que é um ambiente de serviço de aplicativo?](../app-service-web/app-service-app-service-environment-intro.md)
* [Criando um ambiente de serviço de aplicativo](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Criação de aplicativos em um ambiente de serviço de aplicativo](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Criando e usando um balanceador de carga interno com ambientes de serviço de aplicativo](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurando um ambiente de serviço de aplicativo](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Dimensionamento de aplicativos em um ambiente de serviço de aplicativo](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Segurança de rede e arquitetura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Como do

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vídeos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
