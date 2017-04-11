<properties 
    pageTitle="Detalhes de configuração de rede para trabalhar com a rota de Express" 
    description="Detalhes de configuração de rede para executar ambientes de serviço de aplicativo em uma rede Virtual conectada a um circuito de rota expressa." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalhes de configuração de rede para ambientes de serviço de aplicativo com rota expressa 

## <a name="overview"></a>Visão geral ##
Os clientes podem se conectar a uma [Rota expressa do Azure] [ ExpressRoute] circuito para seu infraestrutura de rede virtual, portanto, ampliando sua rede local no Azure.  Um ambiente de serviço de aplicativo pode ser criado em uma sub-rede dessa [rede virtual] [ virtualnetwork] infraestrutura.  Aplicativos em execução no ambiente de serviço de aplicativo, em seguida, podem estabelecer conexões seguras com recursos de back-end acessíveis apenas sobre a conexão de rota expressa.  

Um ambiente de serviço de aplicativo pode ser criado em **qualquer** uma rede virtual do Gerenciador de recursos do Azure, **ou** uma implantação clássica de modelo de rede virtual.  Com uma alteração recente feita no de 2016 de junho, ASEs agora também podem ser implantados em redes virtuais que usam intervalos de endereços público ou espaços de endereço de RFC1918 (ou seja endereços particulares). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Conectividade de rede necessária ##
Há requisitos de conectividade de rede para ambientes de serviço de aplicativo que não pode ser atendida inicialmente em uma rede virtual conectada a uma rota expressa.  Serviço de aplicativo ambientes exige todos os seguintes para funcionar corretamente:


-  Conectividade de rede de saída para pontos de extremidade de armazenamento do Azure em todo o mundo em ambas as portas 80 e 443.  Isso inclui pontos de extremidade localizados na mesma região, como o ambiente do aplicativo de serviço, bem como pontos de extremidade de armazenamento localizado em **outras** regiões Azure.  Pontos de extremidade de armazenamento Azure resolver em domínios DNS a seguir: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.  
-  Conectividade de rede de saída para o serviço de arquivos do Azure na porta 445.
-  Conectividade de rede de saída para pontos de extremidade de banco de dados Sql localizado na mesma região como o ambiente de serviço de aplicativo.  Pontos de extremidade do SQL DB resolver em domínio a seguir: *database.windows.net*.  Isso requer abrindo o acesso às portas 1433, 11999 11000 e 14000-14999.  Para obter mais detalhes, consulte [Este artigo sobre o uso da porta V12 de banco de dados do Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Conectividade de rede de saída para os pontos de extremidade de plano de gerenciamento Azure (pontos de extremidade ASM e ARM).  Isso inclui conectividade de saída para *management.core.windows.net* e *management.azure.com*. 
-  Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  Isso é necessário para oferecer suporte à funcionalidade SSL.
-  A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados os pontos anteriores.  Se esses pontos de extremidade não podem ser resolvidos, tentativas de criação de ambiente de serviço de aplicativo falhará e ambientes de serviço de aplicativo existentes será marcado como não íntegra.
-  Acesso de saída na porta 53 é necessário para a comunicação com servidores DNS.
-  Se houver um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS deve ser acessado da sub-rede contendo o ambiente de serviço de aplicativo. 
-  O caminho de rede de saída não pode percorrer proxies corporativos internos, nem pode ser forçar encapsulado para o local.  Isso altera o endereço NAT eficaz de tráfego de rede de saída do ambiente de serviço de aplicativo.  Alterar o endereço NAT de tráfego de rede de saída de um ambiente serviço de aplicativo causará falhas de conectividade para muitos dos pontos de extremidade listados acima.  Isso resulta em tentativas de criação do ambiente de aplicativo de serviço, bem como anteriormente eficaz, ambientes de serviço de aplicativo sejam marcados como não íntegra.  
-  Acesso de rede para portas necessárias de entrada para ambientes de serviço de aplicativo deve ser permitido conforme descrito neste [artigo][requiredports].

Os requisitos de DNS podem ser atendidos, garantindo uma infraestrutura DNS válida é configurada e mantida para a rede virtual.  Se por algum motivo a configuração do DNS for alterada após a criação de um ambiente de serviço de aplicativo, desenvolvedores podem forçar um ambiente de serviço de aplicativo para retomar a nova configuração de DNS.  Disparar uma reinicialização ambiente sem interrupção usando o ícone "Reiniciar" localizado na parte superior da lâmina de gerenciamento do ambiente de aplicativo de serviço no [portal do Azure] [ NewPortal] fará com que o ambiente retomar a nova configuração de DNS.

Os requisitos de acesso de rede de entrada podem ser atendidos por configurar um [grupo de segurança de rede] [ NetworkSecurityGroups] na sub-rede do ambiente de serviço de aplicativo para permitir o acesso necessário, conforme descrito neste [artigo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Habilitar a conectividade de rede de saída para um ambiente de serviço de aplicativo##
Por padrão, um circuito de rota expressa recém-criado anuncia uma rota padrão que permite a conectividade de Internet de saída.  Com essa configuração um ambiente de serviço de aplicativo poderá se conectar a outros pontos de extremidade Azure.

No entanto, uma configuração de cliente comum é definir seu próprios rota padrão (0.0.0.0/0) que força o tráfego de Internet de saída para fluxo em vez disso, o local.  Este fluxo de tráfego invariavelmente quebras ambientes de serviço de aplicativo porque o tráfego de saída é locais bloqueados ou NAT faria para um conjunto não reconhecido de endereços que já não funcionam com vários pontos de extremidade Azure.

A solução é definir uma (ou mais) rotas definidas pelo usuário (UDRs) na sub-rede que contém o ambiente de serviço de aplicativo.  Um UDR define rotas de sub-rede específicos que serão atendidas em vez da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

- A configuração de rota expressa anuncia 0.0.0.0/0 e por padrão forçar túneis todo o tráfego de saída local.
- O UDR aplicado à sub-rede contendo o ambiente de serviço de aplicativo define 0.0.0.0/0 com um tipo de salto próxima de Internet (um exemplo disto é futuramente para baixo neste artigo).

O efeito combinado destas etapas é que o nível de sub-rede UDR terá precedência sobre a rota expressa forçada túnel garantindo acesso de Internet de saída do ambiente de serviço de aplicativo.

> [AZURE.IMPORTANT] As rotas definidas em UDR **deve** ser específicos o suficiente para precedência sobre quaisquer rotas anunciadas pela configuração rota expressa.  O exemplo a seguir usa o intervalo de endereços ampla 0.0.0.0/0 e como tal pode potencialmente ser acidentalmente substituído por anúncios de rota usando intervalos de endereços mais específicos.
>
>Ambientes de serviço de aplicativo não são compatíveis com configurações de rota expressa **entre-anunciar rotas do caminho aos público para o caminho de correspondência particular**.  Configurações de rota expressa que tem correspondência público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure.  Se esses intervalos de endereços entre anunciado no caminho aos particular, o resultado final é que todos os pacotes de rede de saída da sub-rede do ambiente de serviço de aplicativo será encapsulado forçar a infraestrutura de rede local do cliente.  Este fluxo de rede não é suportado atualmente com ambientes de serviço de aplicativo.  Uma solução para este problema é parar rotas de cruz anúncio do caminho aos público para o caminho de correspondência particular.

Informações básicas sobre rotas definidas pelo usuário estão disponíveis neste [Visão geral][UDROverview].  

Detalhes sobre como criar e configurar rotas definidas pelo usuário está disponível neste [O guia de instruções][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exemplo de configuração de UDR para um ambiente de serviço de aplicativo ##

**Pré-requisitos**

1. Instalar o Azure Powershell na [página Downloads do Azure] [ AzureDownloads] (com data de junho de 2015 ou posterior).  Em "Ferramentas de linha de comando" há um link "Instalar" em "Windows Powershell" que irá instalar os cmdlets do Powershell mais recentes.

2. É recomendável que uma sub-rede exclusiva é criada para uso exclusivo por um ambiente de serviço de aplicativo.  Isso garante que o UDRs aplicados à sub-rede serão aberto somente o tráfego de saída para o ambiente de serviço de aplicativo.
3. **Importante**: não implante o ambiente de serviço de aplicativo até **após** as seguintes etapas de configuração são seguidas.  Isso garante que a conectividade de rede de saída está disponível antes de tentar implantar um ambiente de serviço de aplicativo.

**Etapa 1: Criar uma tabela de rota nomeado**

O trecho a seguir cria uma tabela de rota chamada "DirectInternetRouteTable" na região Oeste EUA Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Etapa 2: Criar uma ou mais rotas na tabela de rotas**

Você precisará adicionar uma ou mais rotas à tabela de rotas para habilitar o acesso de Internet de saída.  

A abordagem recomendada para configurar o acesso de saída com a Internet é definir uma rota para 0.0.0.0/0 conforme mostrado abaixo.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Lembre-se de que 0.0.0.0/0 é um intervalo de endereços ampla e como tal serão substituídas por mais específicos intervalos de endereços anunciados pela rota expressa.  Para iteramos novamente a recomendação anterior, um UDR com uma rota 0.0.0.0/0 deve ser usado em conjunto com uma configuração de ExressRoute que só informa 0.0.0.0/0 também. 

Como alternativa, você pode baixar uma lista abrangente e atualizada de intervalos CIDR em uso por Azure.  O arquivo Xml contendo todos os intervalos de endereços IP do Azure está disponível no [Centro de Download da Microsoft]de[DownloadCenterAddressRanges].  

Observe, porém, que esses intervalos alteram ao longo do tempo, portanto, a necessidade de atualizações manuais periódicas às rotas definidas pelo usuário para manter em sincronia.  Além disso, como há um limite superior padrão de 100 rotas em um único UDR, você precisará "resumir" os intervalos de endereços IP do Azure para ajustá-la dentro do limite de 100 rota, tendo em mente que UDR definido rotas precisam ser mais específico que as rotas anunciado pelo seu rota expressa.  


**Etapa 3: Associar a tabela de rota à sub-rede contendo o ambiente de serviço de aplicativo**

A última etapa de configuração é associar a tabela de rota para a sub-rede onde o ambiente de serviço de aplicativo será implantado.  O comando a seguir associa a "DirectInternetRouteTable" para "ASESubnet" que eventualmente conterá um ambiente de serviço de aplicativo.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Etapa 4: Etapas finais**

Depois que a tabela de rota está vinculada à sub-rede, é recomendável primeiro testar e confirmar o efeito pretendido.  Por exemplo, implantar uma máquina virtual em sub-rede e confirme que:


- O tráfego de saída para pontos de extremidade do Azure e não-Azure mencionado anteriormente neste artigo **não** está fluindo para baixo o circuito rota expressa.  É muito importante verificar esse comportamento, desde que se o tráfego de saída da sub-rede for ainda está sendo forçado encapsulado local, ambiente de serviço de aplicativo de criação sempre falhará. 
- Pesquisas DNS para os pontos de extremidade mencionado anteriormente estão todos resolvendo corretamente. 

Depois que as etapas acima são confirmadas, você precisará excluir a máquina virtual porque a sub-rede precisa estar "vazio" no momento que o ambiente de serviço de aplicativo é criado.
 
Em seguida, prossiga com a criação de um ambiente de serviço de aplicativo!

## <a name="getting-started"></a>Guia de Introdução
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução ao ambiente de serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
