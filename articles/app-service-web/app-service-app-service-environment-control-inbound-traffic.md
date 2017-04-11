<properties 
    pageTitle="Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo" 
    description="Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente de serviço de aplicativo." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
Um ambiente de serviço de aplicativo pode ser criado em **qualquer** uma rede virtual do Gerenciador de recursos do Azure, **ou** uma implantação clássica de modelo de [rede virtual][virtualnetwork].  Uma nova rede virtual e nova sub-rede podem ser definidos no momento em que um ambiente de serviço de aplicativo é criado.  Como alternativa, um ambiente de serviço de aplicativo pode ser criado em uma rede virtual preexistente e sub-rede preexistente.  Com uma alteração recente feita no de 2016 de junho, ASEs agora podem ser implantados em redes virtuais que usam intervalos de endereços público ou espaços de endereço de RFC1918 (ou seja endereços privados).  Para obter mais detalhes sobre a criação de um ambiente de serviço de aplicativo, consulte [como criar um ambiente de serviço de aplicativo][HowToCreateAnAppServiceEnvironment].

Um ambiente de serviço de aplicativo sempre devem ser criado em uma sub-rede porque uma sub-rede fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada atrás upstream dispositivos e serviços, de forma que o tráfego de HTTP e HTTPS só é aceito de endereços IP upstream específicos.

Tráfego de rede de entrada e saída em uma sub-rede é controlado usando um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada requer a sub-rede que contém o ambiente de serviço de aplicativo de grupo de criar regras de segurança de rede em um grupo de segurança de rede e depois atribuir a segurança de rede.

Quando um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para aplicativos no ambiente de serviço de aplicativo é permissão/bloqueio com base em permitir e negar regras definidas no grupo de segurança de rede.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Portas de rede usadas em um ambiente de serviço de aplicativo ##
Antes de bloqueando o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede obrigatórios e opcionais usados por um ambiente de serviço de aplicativo.  Fechar acidentalmente desativar o tráfego para algumas portas pode resultar em perda de funcionalidade em um ambiente de serviço de aplicativo.

A seguir está uma lista de portas usadas por um ambiente de serviço de aplicativo. Todas as portas são **TCP**, a menos que claramente seja observado o contrário:

- 454: **obrigatório porta** usado pela infraestrutura Azure para gerenciar e manter ambientes de serviço de aplicativo por meio de SSL.  Não bloquear o tráfego para esta porta.  Esta porta sempre está associada ao VIP público de um ASE.
- 455: **obrigatório porta** usado pela infraestrutura Azure para gerenciar e manter ambientes de serviço de aplicativo por meio de SSL.  Não bloquear o tráfego para esta porta.  Esta porta sempre está associada ao VIP público de um ASE.
- 80: porta HTTP para tráfego de entrada para aplicativos em execução no aplicativo de serviço planos em um ambiente de serviço de aplicativo padrão.  Em um ASE habilitados para ILB, essa porta está vinculada ao endereço de ILB do ASE.
- 443: porta SSL para tráfego de entrada para aplicativos em execução no aplicativo de serviço planos em um ambiente de serviço de aplicativo padrão.  Em um ASE habilitados para ILB, essa porta está vinculada ao endereço de ILB do ASE.
- 21: canal de controle FTP.  Esta porta pode ser bloqueada com segurança se FTP não está sendo usado.  Em um ASE habilitados para ILB, essa porta pode ser vinculada ao endereço ILB para um ASE.
- 990: canal de controle para FTPS.  Esta porta pode ser bloqueada com segurança se FTPS não está sendo usado.  Em um ASE habilitados para ILB, essa porta pode ser vinculada ao endereço ILB para um ASE.
- 10020 10001: canais de dados FTP.  Como com o canal de controle, essas portas podem ser bloqueadas com segurança se FTP não está sendo usado.  Em um ASE habilitados para ILB, essa porta pode ser vinculada ao endereço ILB do ASE.
- 4016: usado para depuração remota com Visual Studio 2012.  Esta porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitados para ILB, essa porta está vinculada ao endereço de ILB do ASE.
- 4018: usado para depuração remota com Visual Studio 2013.  Esta porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitados para ILB, essa porta está vinculada ao endereço de ILB do ASE.
- 4020: usado para depuração remota com Visual Studio 2015.  Esta porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitados para ILB, essa porta está vinculada ao endereço de ILB do ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída ##
Para um ambiente de serviço de aplicativo funcione corretamente, ele requer acesso de saída para vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE é na seção "Conectividade de rede obrigatório" do artigo [Configuração de rede para rota expressa](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Os ambientes de aplicativo de serviço exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se por algum motivo a configuração do DNS for alterada após a criação de um ambiente de serviço de aplicativo, desenvolvedores podem forçar um ambiente de serviço de aplicativo para retomar a nova configuração de DNS.  Disparar uma reinicialização ambiente sem interrupção usando o ícone "Reiniciar" localizado na parte superior da lâmina de gerenciamento do ambiente de aplicativo de serviço no [portal do Azure] [ NewPortal] fará com que o ambiente retomar a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configuração antecedência antes de criar um ambiente de serviço de aplicativo.  Se a configuração de DNS da rede virtual for alterada enquanto está sendo criado um ambiente de serviço de aplicativo, que resultará em falha do processo de criação do ambiente de aplicativo de serviço.  Em um sentido similar, se existe um servidor DNS personalizado na outra extremidade de um gateway VPN e o servidor DNS está inacessível ou indisponível, o processo de criação do ambiente de serviço de aplicativo também falhará.

## <a name="creating-a-network-security-group"></a>Criando um grupo de segurança de rede ##
Para obter detalhes completos sobre como funcionam os grupos de segurança de rede, consulte as seguintes [informações][NetworkSecurityGroups].  Os detalhes abaixo de toque em realça dos grupos de segurança de rede, com foco na configuração e aplicação de um grupo de segurança de rede para uma sub-rede que contenha um ambiente de serviço de aplicativo.

**Observação:** Grupos de segurança de rede podem ser configurados graficamente usando o [Portal do Azure](https://portal.azure.com) ou através do PowerShell do Azure.

Grupos de segurança de rede primeiro são criados como uma entidade independente associada a uma assinatura. Como os grupos de segurança de rede são criados em uma região Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região como o ambiente de serviço de aplicativo.

A seguir demonstra a criação de um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Após criar um grupo de segurança de rede, uma ou mais regras de segurança de rede são adicionadas a ele.  Desde que o conjunto de regras pode mudar ao longo do tempo, é recomendável espaço check-out de esquema de numeração usado para as prioridades de regra para tornar mais fácil inserir regras adicionais ao longo do tempo.

O exemplo a seguir mostra uma regra que concede explicitamente o acesso às portas de gerenciamento necessários para infraestrutura do Azure para gerenciar e manter um ambiente de serviço de aplicativo.  Observe que todo o tráfego de gerenciamento flua sobre SSL e é protegido por certificados de cliente, então Embora as portas são abertas são inacessíveis por qualquer entidade diferente de infraestrutura de gerenciamento Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Quando bloqueando o acesso a portas 80 e 443 "Ocultar" em um ambiente de serviço de aplicativo atrás upstream dispositivos ou serviços, você precisará saber o endereço IP upstream.  Por exemplo, se você estiver usando um firewall de aplicativo da web (WAF), o WAF terá seu próprio endereço IP (ou endereços) que ele usa quando o tráfego de proxy para um ambiente de serviço de aplicativo downstream.  Você precisará usar este endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um determinado endereço IP upstream explicitamente é permitido.  O endereço *1.2.3.4* é usado como um espaço reservado para o endereço IP de um WAF upstream.  Altere o valor para coincidir com o endereço usado pelo seu dispositivo de upstream ou serviço.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Se suporte a FTP for desejado, as seguintes regras podem ser usadas como um modelo para conceder acesso aos dados e porta de controle FTP portas de canal.  Como FTP é um protocolo com estado, você não poderá rotear o tráfego FTP por meio de um dispositivo de proxy ou firewall HTTP/HTTPS tradicional.  Nesse caso, você precisará definir o *SourceAddressPrefix* para um valor diferente - por exemplo o intervalo de endereços IP do desenvolvedor ou máquinas de implantação na qual FTP clientes estiverem executando. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Observação:** o intervalo de portas de canal de dados pode ser alterados durante o período de visualização.)

Se a depuração remota com o Visual Studio for usado, as regras a seguir demonstram como conceder acesso.  Há uma regra separada para cada versão compatível do Visual Studio como cada versão usa uma porta diferente para depuração remota.  Como com o acesso FTP, tráfego de depuração remoto não pode fluir corretamente por meio de um dispositivo de proxy ou WAF tradicional.  O *SourceAddressPrefix* em vez disso podem ser definidas para o intervalo de endereços IP de máquinas de desenvolvedor executando Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um grupo de segurança de rede para uma sub-rede ##
Um grupo de segurança de rede tem uma regra de segurança padrão que nega acesso a todo o tráfego externo.  O resultado da combinação as regras de segurança de rede descritas acima e a regra de segurança padrão bloqueando o tráfego de entrada, é que somente o tráfego do endereço de origem intervalos associados a uma ação *Permitir* poderão enviar tráfego para aplicativos em execução em um ambiente de serviço de aplicativo.

Depois de um grupo de segurança de rede é preenchido com regras de segurança, ela precisa ser atribuído à sub-rede contendo o ambiente de serviço de aplicativo.  O comando de atribuição referencia tanto o nome da rede virtual onde reside o ambiente de serviço de aplicativo, como o nome da sub-rede onde o ambiente de serviço de aplicativo foi criado.  

O exemplo a seguir mostra um grupo de segurança de rede sendo atribuído a uma rede virtual e sub-rede:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Depois que a atribuição de grupo de segurança de rede é bem sucedida (atribuição é um operações de execução longa e pode demorar alguns minutos para ser concluída), somente o tráfego de entrada *Permitir* regras de correspondência com êxito atingirá aplicativos no ambiente de serviço de aplicativo.

Integridade o exemplo a seguir mostra como remover e, portanto, diferentes associar o grupo de segurança de rede da sub-rede:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais sobre IP-SSL explícito ##
Se um aplicativo estiver configurado com um IP-SSL explícito de endereço (aplicável a ASEs que possuem apenas um VIP público), em vez de usar o endereço IP padrão do ambiente de serviço do aplicativo, HTTP e HTTPS tráfego flui para a sub-rede ao longo de um conjunto diferente de portas diferente de portas 80 e 443.

O par individual de portas usadas por cada endereço IP SSL pode ser encontrado na interface do usuário do portal da lâmina UX de detalhes do ambiente de serviço de aplicativo.  Selecione "todas as configurações"--> "Endereços IP".  A lâmina "Endereços IP" mostra uma tabela de todos os endereços de IP SSL explicitamente configurados para o ambiente de serviço de aplicativo, juntamente com o par de porta especial que é usado para rotear o tráfego HTTP e HTTPS associado a cada endereço IP SSL.  É esse par de porta que precisa ser usado para os parâmetros de DestinationPortRange ao configurar regras em um grupo de segurança de rede.

Quando um aplicativo em um ASE é configurado para usar SSL de IP, clientes externos não verão e não precisa se preocupar sobre o mapeamento de par de porta especial.  O tráfego para os aplicativos fluirá normalmente para o endereço IP SSL configurado.  A tradução ao par porta especial automaticamente acontece internamente durante segmento final da rotear o tráfego para a sub-rede que contém o ASE. 

## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução ao ambiente de serviço de aplicativo][IntroToAppServiceEnvironment]

Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para obter detalhes em torno de uma conexão segura a recurso de back-end de um ambiente de serviço de aplicativo de aplicativos, consulte [uma conexão segura para os recursos de back-end de um ambiente de serviço de aplicativo][SecurelyConnecttoBackend]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
