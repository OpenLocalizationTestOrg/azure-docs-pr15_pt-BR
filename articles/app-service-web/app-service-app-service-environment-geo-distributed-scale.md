<properties 
    pageTitle="Geográfica distribuído escala com ambientes de serviço de aplicativo" 
    description="Saiba como horizontalmente dimensionar aplicativos usando a distribuição geográfica com ambientes de serviço de aplicativo e Gerenciador de tráfego." 
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
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Localização geográfica distribuído escala com ambientes de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
Cenários de aplicativos que exigem muito alta escala podem exceder a capacidade de recursos de computação disponível para uma única implantação de um aplicativo.  Votação aplicativos, eventos esportivos e eventos de entretenimento televised são exemplos de cenários que exigem muito alta escala. Requisitos de alta escala podem ser atendidos dimensionando horizontalmente aplicativos, com várias implantações de aplicativo feitas dentro de uma única região, bem como entre regiões, lidar com os requisitos de carga extrema.

Os ambientes de serviço de aplicativo são uma plataforma ideal para escala horizontal check-out.  Uma vez um aplicativo de serviço ambiente configuração foi selecionada que pode oferecer suporte a uma taxa de solicitação conhecidos, os desenvolvedores podem implantar adicionais ambientes de serviço de aplicativo maneira "ferramenta de corte de cookie" para atingir uma capacidade de carga de pico desejado.

Por exemplo, suponha que um aplicativo em execução em uma configuração de ambiente de aplicativo de serviço foi testado para lidar com solicitações de 20K por segundo (RPS).  Se a capacidade de carga de pico desejado for 100 mil RPS, ambientes de serviço de aplicativo cinco (5) pode ser criado e configurado para garantir que o aplicativo pode lidar com a carga de projetada máxima.

Como os clientes normalmente acessar aplicativos usando um domínio personalizado (ou banido), os desenvolvedores precisam uma maneira de distribuir solicitações de aplicativo em todas as instâncias do ambiente de aplicativo de serviço.  Uma ótima maneira de fazer isso é resolver o domínio personalizado usando um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  O perfil do Gerenciador de tráfego pode ser configurado para apontar para todos os ambientes de serviço de aplicativo individuais.  Gerenciador de tráfego tratará automaticamente clientes distribuindo em todos os ambientes de serviço de aplicativo com base em configurações do perfil do Gerenciador de tráfego de balanceamento de carga.  Essa abordagem funciona independentemente se todos os ambientes de serviço de aplicativo estão implantados em uma única região Azure ou implantados em todo o mundo em várias regiões Azure.

Além disso, como clientes acessar aplicativos através do domínio banido, clientes são cientes do número de ambientes de serviço de aplicativo executando um aplicativo.  Como resultado desenvolvedores podem rapidamente e facilmente adicionar e remover, ambientes de serviço de aplicativo com base na carga de tráfego observado.

O diagrama conceitual abaixo mostra um aplicativo dimensionado horizontalmente em três ambientes de serviço de aplicativo dentro de uma única região.

![Arquitetura conceitual][ConceptualArchitecture] 

O restante deste tópico explica as etapas envolvidas na configuração de uma topologia distribuída para o aplicativo de amostra usando vários ambientes de serviço de aplicativo.

## <a name="planning-the-topology"></a>Planejar a topologia ##
Antes de compilar check-out de um espaço de aplicativo distribuído, é útil para ter algumas informações de peças antecedência.

- **Domínio personalizado para o aplicativo:**  O que é o nome de domínio personalizado que os clientes usarão para acessar o aplicativo?  Para o aplicativo de amostra o nome de domínio personalizado é *www.scalableasedemo.com*
- **Gerenciador de tráfego domínio:**  Um nome de domínio deve ser escolhido ao criar um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  Esse nome será combinado com o sufixo *trafficmanager.net* registrar uma entrada de domínio é gerenciada pelo Gerenciador de tráfego.  Para o aplicativo de exemplo, o nome escolhido é *demonstração scalable ase*.  Como resultado, o nome de domínio completo que é gerenciado pelo Gerenciador de tráfego é *demo.trafficmanager.net scalable ase*.
- **Estratégia para dimensionar o espaço de aplicativo:**  O espaço de aplicativo será distribuído em vários ambientes de serviço de aplicativo em uma única região?  Várias regiões?  Uma mistura-e-correspondência das duas abordagens?  A decisão deve se basear expectativas de origem de tráfego de cliente, bem como quanto o resto da infraestrutura de back-end de suporte de um aplicativo pode dimensionar.  Por exemplo, com um aplicativo de estado de 100%, um aplicativo pode ser amplamente dimensionado usando uma combinação de vários ambientes de serviço de aplicativo por região Azure, multiplicado por ambientes de serviço de aplicativo implantado em várias regiões Azure.  Com 15 + públicos Azure regiões disponíveis à sua escolha, os clientes podem realmente criar um espaço de aplicativo de hyper escala mundial.  Para o aplicativo de exemplo usado neste artigo, três ambientes de serviço de aplicativo foram criado em uma única região Azure (Centro Sul dos EUA).
- **Convenção de nomenclatura para os ambientes de serviço de aplicativo:**  Cada ambiente de serviço de aplicativo requer um nome exclusivo.  Além de um ou dois ambientes de serviço de aplicativo é útil ter uma convenção de nomenclatura para ajudar a identificar cada ambiente de serviço de aplicativo.  Para o aplicativo de amostra uma convenção de nomenclatura simple foi usada.  Os nomes dos três ambientes de serviço de aplicativo são *fe1ase*, *fe2ase*e *fe3ase*.
- **Convenção de nomenclatura para os aplicativos:**  Desde que serão implantadas várias instâncias do aplicativo, é necessário um nome para cada instância do aplicativo implantado.  Um recurso de pouco conhecido mas muito conveniente de ambientes de serviço de aplicativo é que o mesmo nome de aplicativo pode ser usado em vários ambientes de serviço de aplicativo.  Como cada ambiente de serviço de aplicativo tem um sufixo de domínio exclusivo, os desenvolvedores podem optar por usar novamente o mesmo nome de aplicativo em cada ambiente.  Por exemplo um desenvolvedor pode ter aplicativos nomeados da seguinte maneira: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  Para o aplicativo de amostra apesar cada instância do aplicativo também tem um nome exclusivo.  Os nomes de instância de aplicativo usados são *webfrontend1*, *webfrontend2*e *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Configurando o perfil do Gerenciador de tráfego ##
Depois de várias instâncias de um aplicativo são implantadas em vários ambientes de serviço de aplicativo, as instâncias de aplicativo individuais podem ser registradas com o Gerenciador de tráfego.  Para o aplicativo de amostra um gerente de tráfego perfil é necessária para *demo.trafficmanager.net scalable ase* que podem rotear clientes para qualquer um dos seguintes instâncias de aplicativo implantado:

- **webfrontend1.fe1ase.p.azurewebsites.net:**  Uma instância do aplicativo amostra implantado no ambiente de serviço de aplicativo primeiro.
- **webfrontend2.fe2ase.p.azurewebsites.net:**  Uma instância do aplicativo amostra implantado no segundo ambiente de serviço de aplicativo.
- **webfrontend3.fe3ase.p.azurewebsites.net:**  Uma instância do aplicativo amostra implantado no ambiente de serviço de aplicativo de terceiro.

A maneira mais fácil para registrar vários serviços de aplicativo do Azure pontos de extremidade, todos em execução na **mesma** região Azure, é com o Powershell [Gerenciador de tráfego do Gerenciador de recursos de Azure suporte][ARMTrafficManager].  

A primeira etapa é criar um perfil de Gerenciador de tráfego do Azure.  O código a seguir mostra como o perfil foi criado para o aplicativo de exemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe como o parâmetro *RelativeDnsName* foi definido como *demonstração scalable ase*.  Isso é como o nome de domínio *demo.trafficmanager.net scalable ase* é criado e associado a um perfil do Gerenciador de tráfego.

O parâmetro *TrafficRoutingMethod* define a Gerenciador de tráfego usará para determinar como distribuir a carga de cliente em todos os pontos de extremidade disponíveis de política de balanceamento de carga.  Neste exemplo o *ponderado* método foi escolhido.  Isso resultará em solicitações de cliente sendo distribuídas em todos os pontos de extremidade do aplicativo registrado com base nos níveis de importância relativas associadas a cada ponto de extremidade. 

Com o perfil criado, cada instância do aplicativo é adicionada ao perfil como um ponto de extremidade Azure nativo.  O código abaixo busca uma referência para cada aplicativo da web de front-end e depois adiciona cada aplicativo como um ponto de extremidade do Gerenciador de tráfego por meio de parâmetro *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Observe como há uma chamada para *Adicionar AzureTrafficManagerEndpointConfig* para cada instância de aplicativo individuais.  O parâmetro *TargetResourceId* em cada comando do Powershell faz referência a uma das três instâncias aplicativo implantado.  O perfil do Gerenciador de tráfego será distribuir a carga em todos os três pontos de extremidade registrados no perfil.

Todos os três pontos de extremidade usam o mesmo valor (10) para o parâmetro de *espessura* .  Isso resulta em solicitações de cliente de difusão do Gerenciador de tráfego em todas as instâncias de aplicativo três igualmente. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontando domínio personalizado do aplicativo no domínio Gerenciador de tráfego ##
A etapa final necessária é apontar o domínio personalizado do aplicativo no domínio Gerenciador de tráfego.  Para o aplicativo de amostra, isso significa apontando *www.scalableasedemo.com* *demo.trafficmanager.net scalable ase*.  Esta etapa deve ser concluído com o registrador que gerencia o domínio personalizado.  

Usando ferramentas de gerenciamento de seu registrador domínio, um CNAME registros precisa ser criado que aponta o domínio personalizado no domínio Gerenciador de tráfego.  A imagem abaixo mostra um exemplo da aparência essa configuração CNAME:

![CNAME do domínio personalizado][CNAMEforCustomDomain] 

Embora não coberta neste tópico, lembre-se de que cada instância de aplicativo individuais precisa ter o domínio personalizado registrado com ele também.  Caso contrário, se uma solicitação facilita a uma instância de aplicativo e o aplicativo não tem o domínio personalizado registrado com o aplicativo, a solicitação falhará.  

Neste exemplo, o domínio personalizado é *www.scalableasedemo.com*e cada instância do aplicativo tem o domínio personalizado associado a ele.

![Domínio personalizado][CustomDomain] 

Para um resumo de registrar um domínio personalizado com aplicativos de serviço de aplicativo do Azure, consulte o seguinte artigo sobre como [registrar domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentar a topologia distribuído ##
O resultado final da configuração do Gerenciador de tráfego e DNS é que as solicitações de *www.scalableasedemo.com* irá fluir por sequência a seguir:

1. Um navegador ou dispositivo fará uma pesquisa DNS para *www.scalableasedemo.com*
2. A entrada de CNAME do registrador faz com que a pesquisa de DNS ser redirecionado ao Gerenciador de tráfego do Azure.
3. Uma pesquisa DNS é feita para *demo.trafficmanager.net scalable ase* em relação a um dos servidores DNS de tráfego Gerenciador do Azure.
4. Com base em política (o parâmetro *TrafficRoutingMethod* usado anteriormente ao criar o perfil do Gerenciador de tráfego) de balanceamento de carga, Gerenciador de tráfego selecione um dos pontos de extremidade configurados e retornar o FQDN do ponto de extremidade para o navegador ou dispositivo.
5.  Como o FQDN do ponto de extremidade é a Url de uma instância do aplicativo em execução em um ambiente de serviço de aplicativo, o navegador ou dispositivo solicitará um servidor DNS da Microsoft Azure para resolver o FQDN para um endereço IP. 
6. O navegador ou dispositivo enviará a solicitação HTTP/S para o endereço IP.  
7. A solicitação chegarão em uma das instâncias do aplicativo em execução em um dos ambientes de serviço de aplicativo.

A imagem de console abaixo mostra uma pesquisa DNS do domínio personalizado do aplicativo de exemplo com êxito Resolvendo para uma instância de aplicativo executando um dos três ambientes de serviço de aplicativo de amostra (no caso o segundo dos três ambientes de serviço de aplicativo):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informações e Links adicionais ##
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Documentação sobre o Powershell [Gerenciador de tráfego do Gerenciador de recursos de Azure suporte][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
