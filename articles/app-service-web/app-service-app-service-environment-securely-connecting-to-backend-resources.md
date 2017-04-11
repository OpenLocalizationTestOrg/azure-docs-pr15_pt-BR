<properties 
    pageTitle="Fazer uma conexão segura para os recursos de back-end de um ambiente de serviço de aplicativo" 
    description="Saiba mais sobre como conectar-se com segurança para os recursos de back-end de um ambiente de serviço de aplicativo." 
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

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Fazer uma conexão segura para os recursos de back-end de um ambiente de serviço de aplicativo #

## <a name="overview"></a>Visão geral ##
Como um ambiente de serviço de aplicativo é sempre criado em **um de** uma rede virtual do Gerenciador de recursos do Azure, **ou** uma implantação clássica de modelo de [rede virtual][virtualnetwork], conexões de saída de um ambiente de serviço de aplicativo para outros recursos de back-end podem fluir exclusivamente pela rede virtual.  Com uma alteração recente feita no de 2016 de junho, ASEs também podem ser implantados em redes virtuais que usam intervalos de endereços público ou espaços de endereço de RFC1918 (ou seja endereços particulares).  

Por exemplo, pode haver um SQL Server em execução em um cluster de máquinas virtuais com porta 1433 bloqueado.  O ponto de extremidade pode ser ACLd para permitir apenas o acesso de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos de extremidade confidenciais podem executar no local e estar conectados ao Azure por meio de qualquer um dos [to-Site] [ SiteToSite] ou [Rota expressa do Azure] [ ExpressRoute] conexões.  Como resultado, somente recursos em redes virtuais conectados ao-to-Site ou rota expressa túneis poderão acessar pontos de extremidade de local.

Para todos esses cenários, aplicativos em execução em um ambiente de serviço de aplicativo poderá conectar-se com segurança para os vários servidores e recursos.  O tráfego de saída de aplicativos em execução em um ambiente de serviço de aplicativo para pontos de extremidade na mesma rede virtual privados (ou conectado à mesma rede virtual), será apenas fluxo pela rede virtual.  Não, o tráfego de saída para pontos de extremidade particulares fluirá na Internet pública.

Uma limitação se aplica ao tráfego de saída de um ambiente de serviço de aplicativo para pontos de extremidade dentro de uma rede virtual.  Ambientes de serviço de aplicativo não pode acessar pontos de extremidade de máquinas virtuais localizados na **mesma** sub-rede que o ambiente de serviço de aplicativo.  Isso normalmente não deve ser um problema desde ambientes de serviço de aplicativo sejam implantado em uma sub-rede reservada para uso exclusivo por apenas o ambiente de serviço de aplicativo.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída ##
Para um ambiente de serviço de aplicativo funcione corretamente, ele requer acesso de saída para vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE é na seção "Conectividade de rede obrigatório" do artigo [Configuração de rede para rota expressa](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Os ambientes de aplicativo de serviço exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se por algum motivo a configuração do DNS for alterada após a criação de um ambiente de serviço de aplicativo, desenvolvedores podem forçar um ambiente de serviço de aplicativo para retomar a nova configuração de DNS.  Disparar uma reinicialização ambiente sem interrupção usando o ícone de "Reinicialização" localizado na parte superior da lâmina de gerenciamento do ambiente de aplicativo de serviço no portal fará com que o ambiente retomar a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configuração antecedência antes de criar um ambiente de serviço de aplicativo.  Se a configuração de DNS da rede virtual for alterada enquanto está sendo criado um ambiente de serviço de aplicativo, que resultará em falha do processo de criação do ambiente de aplicativo de serviço.  Em um sentido similar, se existe um servidor DNS personalizado na outra extremidade de um gateway VPN e o servidor DNS está inacessível ou indisponível, o processo de criação do ambiente de serviço de aplicativo também falhará.

## <a name="connecting-to-a-sql-server"></a>Conectar a um servidor SQL
Uma configuração comum do SQL Server tem um ponto de extremidade escutar na porta 1433:

![Ponto de extremidade do SQL Server][SqlServerEndpoint]

Há duas abordagens para restringir o tráfego para esta empresa:


- [Listas de controle de acesso de rede] [ NetworkAccessControlLists] (rede ACLs)

- [Grupos de segurança de rede][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Restringindo o acesso com uma rede ACL

Porta 1433 pode ser protegida usando uma lista de controle de acesso de rede.  O exemplo abaixo brancas cliente endereços provenientes dentro de uma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Aplicativos em execução no ambiente de serviço de aplicativo na mesma rede virtual como o SQL Server será capazes de conectar-se a instância do SQL Server usando o endereço IP **VNet interno** para máquina virtual do SQL Server.  

A cadeia de conexão de exemplo abaixo referências o SQL Server usando o endereço IP particular.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Embora a máquina virtual tenha um ponto de extremidade público, usando o endereço IP público de tentativas de conexão serão rejeitadas devido a ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringindo o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados para máquinas virtuais individuais ou para uma sub-rede que contém máquinas virtuais.

Primeiro, um grupo de segurança de rede precisa ser criada:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringindo o acesso a apenas VNet tráfego interno é muito simple com um grupo de segurança de rede.  As regras padrão em um grupo de segurança de rede só permitem o acesso de outros clientes de rede na mesma rede virtual.

Como resultado bloqueando o acesso ao SQL Server é tão simple quanto a aplicação de um grupo de segurança de rede com suas regras padrão para as máquinas virtuais executando o SQL Server ou a sub-rede que contém as máquinas virtuais.

O exemplo a seguir se aplica a um grupo de segurança de rede à sub-rede contendo:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
O resultado final é um conjunto de regras de segurança bloquear o acesso externo, permitindo o acesso interno VNet:

![Regras de segurança de rede padrão][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Guia de Introdução
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução ao ambiente de serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes em torno de controlar o tráfego de entrada para seu ambiente de serviço de aplicativo, consulte [controlar o tráfego de entrada para um ambiente de serviço de aplicativo][ControlInboundASE]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
