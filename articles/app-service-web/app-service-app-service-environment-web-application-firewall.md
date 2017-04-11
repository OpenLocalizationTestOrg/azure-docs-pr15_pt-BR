<properties 
    pageTitle="Configurando um Firewall de aplicativo da Web (WAF) para o ambiente de serviço de aplicativo" 
    description="Saiba como configurar um firewall de aplicativo web na frente do seu ambiente de serviço de aplicativo." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurando um Firewall de aplicativo da Web (WAF) para o ambiente de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
Web firewalls de aplicativo como o [WAF Barracuda Azure](https://www.barracuda.com/programs/azure) que está disponível na Ajuda do [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) seguro de que tráfego da web para bloquear inclusões de SQL, script entre sites, carregamentos de malware & aplicativo DDoS e outros ataques de entrada de seus aplicativos web por meio da inspeção. Ele também analisa as respostas dos servidores web de back-end para prevenção de perda de dados (DLP). Isso combinado com o isolamento e a escala adicionais fornecidos pelo aplicativo de serviço ambientes, fornece um ambiente ideal para aplicativos de web crítico de negócios de host que precisa suportar solicitações maliciosos e tráfego de alto volume.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Configuração ##
Para este documento que vamos configurar nosso ambiente de serviço de aplicativo atrás carregar vários equilibrada instâncias de Barracuda WAF para que apenas o tráfego da WAF pode acessar o ambiente de serviço de aplicativo e ele não poderá ser acessado da DMZ. Também teremos Gerenciador de tráfego do Azure na frente de nossas instâncias Barracuda WAF para balanceamento de carga entre Azure data centers e regiões. Um diagrama de alto nível da configuração ficaria o que é mostrado abaixo.

![Arquitetura][Architecture] 

> Observação: Com a introdução do [ILB suporte para o ambiente de serviço de aplicativo](app-service-environment-with-internal-load-balancer.md), você pode configurar o ASE para ser inacessíveis da DMZ e estar disponível apenas para a rede privada. 

## <a name="configuring-your-app-service-environment"></a>Configurar o ambiente de serviço de aplicativo ##
Para configurar um ambiente de serviço de aplicativo consulte [nossa documentação](app-service-web-how-to-create-an-app-service-environment.md) sobre o assunto. Quando você tiver um ambiente de serviço de aplicativo criado, você pode criar [Aplicativos Web](app-service-web-overview.md), [Aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md) e [Aplicativos Mobile](../app-service-mobile/app-service-mobile-value-prop.md) nesse ambiente que será protegido por trás do WAF podemos configurar na próxima seção.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurar seu serviço de nuvem WAF Barracuda ##
Barracuda tem um [artigo detalhado](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sobre como implantar sua WAF em uma máquina virtual no Azure. Mas, como podemos desejado redundância e não apresenta um único ponto de falha, que deseja implantar pelo menos 2 VMs de instância WAF no serviço de nuvem mesmo quando estas instruções.

### <a name="adding-endpoints-to-cloud-service"></a>Adicionar pontos de extremidade para o serviço em nuvem ###
Quando você tiver 2 ou mais ocorrências de máquina virtual WAF em seu serviço de nuvem, você pode usar o [portal do Azure](https://portal.azure.com/) para adicionar pontos de extremidade HTTP e HTTPS que são usados pelo seu aplicativo, conforme mostrado na imagem abaixo.

![Configurar o ponto de extremidade][ConfigureEndpoint]

Se seus aplicativos usam outros pontos de extremidade, certifique-se de adicioná-los a esta lista também. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurando Barracuda WAF por meio de seu Portal de gerenciamento ###
Barracuda WAF usa TCP porta 8000 para configuração por meio de seu portal de gerenciamento. Como temos várias instâncias das VMs WAF você precisará Repita essas etapas para cada instância de máquina virtual. 


> Observação: Depois que terminar com a configuração de WAF, remova o ponto de extremidade TCP/8000 a todas as suas VMs WAF para manter sua WAF segura.

Adicione o ponto de extremidade de gerenciamento, conforme mostrado na imagem abaixo para configurar seu WAF Barracuda.

![Adicionar ponto de extremidade de gerenciamento][AddManagementEndpoint]
 
Use um navegador para navegar até o ponto de extremidade de gerenciamento no seu serviço de nuvem. Se seu serviço de nuvem é chamado test.cloudapp.net, você poderia acessar esse ponto de extremidade navegando até http://test.cloudapp.net:8000. Você deve ver uma página de login, como abaixo que você pode fazer logon usando credenciais que você especificou na fase de configuração de máquina virtual WAF.

![Página de Login de gerenciamento][ManagementLoginPage]

Uma vez que você login você verá um painel como na imagem abaixo que apresentará estatísticas básicas sobre a proteção de WAF.

![Painel de gerenciamento][ManagementDashboard]

Clicando na guia Serviços permitirá que você a configurar seu WAF para serviços que ele está protegendo. Para obter mais detalhes sobre como configurar seu WAF Barracuda você pode consultar [sua documentação](https://techlib.barracuda.com/waf/getstarted1). No exemplo abaixo de um aplicativo Web do Azure servir tráfego em HTTP e HTTPS foi configurado.

![Gerenciamento de adicionar serviços][ManagementAddServices]

> Observação: Dependendo de como seus aplicativos são configurados e quais recursos estão sendo usados no seu ambiente de serviço de aplicativo, você precisará encaminhar o tráfego para TCP portas diferente de 80 e 443, por exemplo, se você tiver a configuração de IP SSL para um aplicativo Web. Para uma lista de portas de rede usadas em ambientes de serviço de aplicativo, consulte a seção de portas de rede [documentação de tráfego de entrada de controle](app-service-app-service-environment-control-inbound-traffic.md) .

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurando o Gerenciador de tráfego do Microsoft Azure (opcional) ##
Se seu aplicativo está disponível em várias regiões, então você gostaria de carregar saldo-las atrás do [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Para fazê-lo, você pode adicionar um ponto de extremidade no [portal clássico Azure](https://manage.azure.com) usando o nome de serviço de nuvem para sua WAF no perfil do Gerenciador de tráfego, conforme mostrado na imagem abaixo. 

![Ponto de extremidade do Gerenciador de tráfego][TrafficManagerEndpoint]

Se seu aplicativo requer autenticação, certifique-se de que você tem alguns recursos que não exigem qualquer autenticação tráfego Gerenciador de ping para a disponibilidade de seu aplicativo. Você pode configurar a URL na seção Configurar no [portal clássico Azure](https://manage.azure.com) conforme mostrado abaixo.

![Configurar o Gerenciador de tráfego][ConfigureTrafficManager]

Para encaminhar o ping do Gerenciador de tráfego de sua WAF para seu aplicativo, você precisa configurar traduções de site no seu WAF Barracuda para encaminhar o tráfego ao seu aplicativo, conforme mostrado no exemplo abaixo.

![Traduções de site][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Protegendo o tráfego para o ambiente de serviço de aplicativo usando grupos de segurança de rede (NSG)##
Siga a [documentação de tráfego de entrada de controle](app-service-app-service-environment-control-inbound-traffic.md) para obter detalhes sobre como restringir o tráfego para seu ambiente de serviço de aplicativo da WAF apenas usando o endereço de VIP do seu serviço de nuvem. Aqui está um exemplo de comando do Powershell para realizar esta tarefa para a porta TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Substitua o SourceAddressPrefix com o endereço IP Virtual (VIP) do serviço de nuvem do seu WAF.

> Observação: O VIP do seu serviço de nuvem mudará quando você exclua e recrie o serviço de nuvem. Certifique-se de atualizar o endereço IP no grupo de recursos de rede feito isso. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
