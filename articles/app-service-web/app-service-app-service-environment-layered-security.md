<properties 
    pageTitle="Arquitetura de segurança em camadas com ambientes de serviço de aplicativo" 
    description="Implementando uma arquitetura de segurança em camadas com ambientes de serviço de aplicativo." 
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
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementar uma arquitetura de segurança em camadas com ambientes de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
 
Como os ambientes de serviço de aplicativo fornece um ambiente de tempo de execução isolados implantado em uma rede virtual, os desenvolvedores podem criar uma arquitetura de segurança em camadas fornecendo diferentes níveis de acesso de rede para cada nível de aplicativo físico.

Um desejo comum é ocultar API back-ends acesso geral de Internet e só permitir APIs para ser chamado por aplicativos web upstream.  [Grupos de segurança (NSGs) de rede] [ NetworkSecurityGroups] pode ser usado em sub-redes contendo ambientes de serviço de aplicativo para restringir o acesso público a API aplicativos.

O diagrama a seguir mostra uma arquitetura de exemplo com um aplicativo de WebAPI com base implantado em um ambiente de serviço de aplicativo.  Três instâncias de aplicativo web separado, implantadas em três ambientes separada de serviço do aplicativo, fazem chamadas de back-end para o mesmo aplicativo WebAPI.

![Arquitetura conceitual][ConceptualArchitecture] 

Nos sinais de adição verdes indicam que o grupo de segurança de rede na sub-rede contendo "apiase" permite chamadas de entrada de aplicativos web upstream, como bem chamadas de si mesmo.  No entanto o mesmo grupo de segurança de rede nega explicitamente o acesso ao tráfego de entrada geral da Internet. 

O restante deste tópico explica as etapas necessárias para configurar o grupo de segurança de rede na sub-rede contendo "apiase".

## <a name="determining-the-network-behavior"></a>Determinando o comportamento de rede ##
Para saber quais regras de segurança de rede são necessários, você precisa determinar quais clientes de rede terão permissão para acessar o ambiente de serviço de aplicativo que contém o aplicativo de API e quais clientes serão bloqueados.

Desde a [grupos de segurança de rede (NSGs)] [ NetworkSecurityGroups] são aplicadas às sub-redes e ambientes de serviço de aplicativo é implantado em sub-redes, as regras contidas em um NSG se aplicam a **todos os** aplicativos em execução em um ambiente de serviço de aplicativo.  Usando a arquitetura de amostra neste artigo, uma vez que um grupo de segurança de rede é aplicado à sub-rede contendo "apiase", todos os aplicativos em execução no ambiente de serviço de aplicativo "apiase" serão protegidos pelo mesmo conjunto de regras de segurança. 

- **Determinar o endereço IP de saída de chamadores upstream:**  O que é o endereço IP ou endereços dos chamadores upstream?  Esses endereços deverão ser explicitamente permitidas acesso a NSG.  Como chamadas entre ambientes de serviço do aplicativo são consideradas chamadas "Internet", isso significa que o endereço IP de saída atribuído a cada um dos três ambientes de serviço de aplicativo upstream precisa ser permitidas acesso a NSG para a sub-rede "apiase".   Para obter mais detalhes sobre como determinar o endereço IP de saída para aplicativos em execução em um ambiente de serviço de aplicativo, consulte a [Arquitetura de rede] [ NetworkArchitecture] artigo de visão geral.
- **O aplicativo de API de back-end precisarão chamar a mesmo?**  Um ponto às vezes ignorado e sutil é o cenário onde o aplicativo de back-end precisa chamar a mesmo.  Se um aplicativo de API de back-end em um ambiente do serviço de aplicativo precisa chamar a mesmo, isso também será tratado como uma chamada de "Internet".  A arquitetura de amostra isso requer permitir o acesso do endereço IP saído do "apiase" ambiente de serviço de aplicativo também.

## <a name="setting-up-the-network-security-group"></a>Configurando o grupo de segurança de rede ##
Depois que o conjunto de endereços IP de saída são conhecidas, a próxima etapa é criar um grupo de segurança de rede.  Grupos de segurança de rede podem ser criados para ambos os gerente de recursos baseados em redes virtuais, bem como redes virtuais clássicas.  Os exemplos abaixo mostram criando e configurando um NSG em uma rede virtual clássica usando o Powershell.

Para a arquitetura de amostra, os ambientes estão localizados no Centro Sul dos EUA, para que um NSG vazia é criada na região:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro explícita permitir regra é adicionada para a infraestrutura de gerenciamento Azure conforme mencionado no artigo de [tráfego] [ InboundTraffic] para ambientes de serviço de aplicativo.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Em seguida, duas regras são adicionadas ao permitir chamadas de HTTP e HTTPS a partir do ambiente de serviço de aplicativo de upstream primeiro ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Limpar e repita para o segundo e terceiro upstream aplicativo ambientes de serviço ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por fim, conceda acesso para o endereço IP de saída do ambiente de serviço de aplicativo da API de back-end para que ele possa chamar novamente para si mesmo.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Não há outras regras de segurança de rede precisam ser configurado porque cada NSG tem um conjunto de regras padrão que bloqueiam o acesso de entrada da Internet por padrão.

A lista completa de regras no grupo de segurança de rede são mostradas abaixo.  Observe como a última regra, que é realçada, bloqueia o acesso de entrada de todos os chamadores diferente daquelas que receberam explicitamente acesso.

![Configuração de NSG][NSGConfiguration] 

A etapa final é aplicar o NSG à sub-rede que contém o ambiente de serviço de aplicativo "apiase".  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à sub-rede, apenas três upstream aplicativo serviço ambientes e o ambiente de serviço de aplicativo contendo API back-end, são permitidas para ligar para o ambiente de "apiase".


## <a name="additional-links-and-information"></a>Informações e Links adicionais ##
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Informações sobre [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md). 

Noções básicas sobre [endereços IP de saída] [ NetworkArchitecture] e ambientes de serviço de aplicativo.

[Portas de rede] [ InboundTraffic] usado pelo ambientes de serviço de aplicativo.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
