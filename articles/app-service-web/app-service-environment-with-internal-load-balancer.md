<properties
    pageTitle="Criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo | Microsoft Azure"
    description="Criar e usar um ASE com um ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Usando um balanceador de carga interno com um ambiente de serviço de aplicativo #

O recurso de Environments(ASE) de serviço de aplicativo é uma opção de serviço Premium de serviço de aplicativo do Azure que fornece um recurso de configuração avançada que não está disponível nos carimbos de vários locatários.  O recurso de ASE essencialmente implanta o serviço de aplicativo do Azure no seu Network(VNet) Virtual do Azure.  Para obter uma compreensão maior dos recursos oferecidos pelo ambientes de serviço de aplicativo ler o [o que é um ambiente de serviço de aplicativo] [ WhatisASE] documentação.  Se você não souber os benefícios de operando em um VNet leia as [Perguntas Frequentes de rede Virtual Azure][virtualnetwork].  


## <a name="overview"></a>Visão geral ##


Um ASE pode ser implantado com um ponto de extremidade acessíveis de internet ou com um endereço IP em seu VNet.  Para definir o endereço IP para um endereço de VNet você precisa implantar sua ASE com um Balancer(ILB) de carga interno.  Quando seu ASE é configurado com uma ILB você fornecer:

- seu próprio domínio ou subdomínio.  Para tornar mais fácil, este documento considera subdomínio, mas você pode configurá-lo de qualquer forma.  
- o certificado usado para HTTPS
- Gerenciamento de DNS para o subdomínio.  


Em retorno, você pode fazer coisas como:

- aplicativos de intranet do host, como linha de aplicativos de negócios, com segurança na nuvem que você acessa através de um Site a Site ou ExpressRoute VPN
- aplicativos de host na nuvem que não estejam listados em servidores DNS públicos
- Crie aplicativos de back-end de internet isolada que seus aplicativos de front-end podem integrar com segurança


#### <a name="disabled-functionality"></a>Funcionalidade desativada ####

Há algumas coisas que você não pode fazer ao usar um ASE ILB.  Esses itens incluem:

- usando IPSSL
- atribuir endereços IP aos aplicativos específicos
- comprar e usar um certificado com um aplicativo por meio do portal.  Claro ainda pode obter certificados diretamente com uma autoridade de certificação e usá-lo com seus aplicativos, não por meio do portal do Azure.


## <a name="creating-an-ilb-ase"></a>Criando um ASE ILB ##

Criar um ASE ILB não é muito diferente da criação de um ASE normalmente.  Para uma discussão mais aprofundada sobre como criar um ASE leia [como criar um ambiente de serviço de aplicativo][HowtoCreateASE].  O processo para criar um ASE ILB é a mesma entre criar um VNet durante a criação de ASE ou selecionando um VNet preexistente.  Para criar um ASE ILB: 

1.  No portal do Azure, selecione **Novo -> Web + Mobile -> ambiente de serviço de aplicativo**
2.  Selecione sua assinatura
3.  Selecione ou crie um grupo de recursos
4.  Selecione ou crie um VNet
5.  Criar uma sub-rede se selecionando um VNet
6.  Selecione **Virtual/local de rede-> Configuração de VNet** e defina o tipo de VIP como interna
7.  Fornecer o nome de subdomínio (este será o subdomínio usado para aplicativos criados neste ASE)
8.  Selecione Ok e, em seguida, criar


![][1]


Dentro do blade de rede Virtual, há uma opção de configuração de VNet.  Isso permite que você selecione entre um VIP externos ou VIP interno.  O padrão é externo.  Se você tiver-definida como externo seu ASE usará um VIP acessíveis de internet.  Se você selecionar interno, seu ASE será configurado com um ILB em um endereço IP dentro sua VNet.  


Depois de selecionar interno, a capacidade de adicionar mais endereços IP para seu ASE é removida, e em vez disso, você precisará fornecer o subdomínio do ASE.  Em um ASE com um VIP externo no nome do ASE é usado no subdomínio para aplicativos criados em que ASE.  
Se seu ASE era chamado ***contosotest*** e seu aplicativo em que era chamado de ASE ***mytest*** , em seguida, o subdomínio seria o formato ***contosotest.p.azurewebsites.net*** e a URL para esse aplicativo seria ***mytest.contosotest.p.azurewebsites.net***.  
Se você definir o tipo de VIP como interna, seu nome de ASE não é usado no subdomínio para o ASE.  Você pode especificar o subdomínio explicitamente.  Se um subdomínio foi ***contoso.corp.net*** e tiver feito um aplicativo em que ASE chamado ***timereporting*** a URL para esse aplicativo seria ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ASE ILB ##

Criando um aplicativo em um ASE ILB é a mesma que a criação de um aplicativo em um ASE normalmente.  

1. No portal do Azure, selecione **Novo -> Web + Mobile -> Web** ou **celular** ou **API do aplicativo**
2. Insira o nome do aplicativo
2. Selecione assinatura
3. Selecione ou crie um grupo de recursos
4. Selecione ou crie Plan(ASP) de serviço do aplicativo.  Se, em seguida, criando um novo ASP selecione seu ASE como o local e selecione o pool de trabalho que deseja que o ASP seja criado no.  Quando você cria o ASP você seleciona seu ASE como o local e o pool de trabalho.  Quando você especifica o nome do aplicativo, você verá que o subdomínio sob seu nome de aplicativo é substituído pelo subdomínio para seu ASE.   
5. Selecione criar.  Você deve selecionar a caixa de seleção **Fixar em dashboard** se quiser que o aplicativo em seu painel.  

![][2]


Sob o nome do aplicativo, o nome de subdomínio for atualizado para refletir o subdomínio do seu ASE.  


## <a name="post-ilb-ase-creation-validation"></a>Validação de criação de ILB ASE postagem ##

Um ASE ILB é um pouco diferente que os não - ILB ASE.  Como já observado necessárias para gerenciar seu próprio DNS e você também terá que fornecer o seu próprio certificado para conexões HTTPS.  


Depois de criar seu ASE você notará que o subdomínio mostra o subdomínio que você especificou e há um novo item no menu de **configuração** chamado **ILB certificado**.  O ASE é criado com um certificado auto-assinado que torna mais fácil testar HTTPS.  O portal informará que você precisa para fornecer o seu próprio certificado para HTTPS, mas esta é a unidade que você tenha um certificado que acompanha seu subdomínio.  

![][3]


Se você está simplesmente experimentando coisas e não sabe como criar um certificado, você pode usar o aplicativo de console IIS MMC para criar um certificado auto-assinado.  Depois que ela é criada, você pode exportá-lo como um arquivo. pfx e carregue-o na interface de usuário de certificado ILB. Quando você acessa um site protegido com um certificado autoassinado, seu navegador você terá um aviso de que o site que você está acessando não é seguro devido a impossibilidade de validar o certificado.  Se você quiser evitar que o aviso é necessário um certificado corretamente assinado que corresponde a um subdomínio e tem uma cadeia de confiança que é reconhecida pelo seu navegador.

![][6]

Se você quiser experimentar o fluxo com seus próprios certificados e testar o acesso HTTP e HTTPS ao seu ASE:

1.  Acesse a interface de usuário do ASE depois ASE é criada **ASE -> Configurações -> certificados de ILB**
2.  Definir o certificado ILB selecionando arquivo pfx de certificado e forneça a senha.  Esta etapa leva algum tempo para processar e a mensagem que uma operação de escala está em andamento será mostrada.
3.  Obter o endereço ILB de seu ASE (**ASE -> Propriedades -> endereço IP Virtual**)
4.  Criar um aplicativo web no ASE após a criação 
5.  Criar uma máquina virtual se você não tiver uma em que VNET (não está na mesma sub-rede a quebra ASE ou itens)
6.  Configurar o DNS para o subdomínio.  Você pode usar um caractere curinga com um subdomínio no seu DNS ou se você quiser fazer alguns testes simples, editar o arquivo de hosts em sua máquina virtual para definir o nome do aplicativo da web para endereço IP VIP.  Se seu ASE tinha o nome de subdomínio. ilbase.com e feitas a mytestapp de aplicativo web para que ele seria ser endereçado ao mytestapp.ilbase.com depois definir essa opção em seu arquivo hosts.  (No Windows o arquivo hosts está no C:\Windows\System32\drivers\etc\)
7.  Use um navegador em que máquina virtual e vá para http://mytestapp.ilbase.com (ou tudo o que é o nome do aplicativo web com um subdomínio)
8.  Use um navegador em que máquina virtual e vá para https://mytestapp.ilbase.com que você precisará aceitar a falta de segurança se usando um certificado auto-assinado.  


O endereço IP do seu ILB está listado em suas propriedades como o endereço IP Virtual

![][4]


## <a name="using-an-ilb-ase"></a>Usando um ASE ILB ##

#### <a name="network-security-groups"></a>Grupos de segurança de rede ####

Um ASE ILB habilita isolamento de rede para seus aplicativos, como os aplicativos não estão acessíveis ou até mesmo conhecidos pela internet.  Isso é excelente para hospedar sites intranet como linha de aplicativos de negócios.  Quando você precisar restringir o acesso mesmo ainda mais você ainda pode usar Groups(NSGs) de segurança de rede para controlar o acesso no nível da rede. 


Se você quiser usar NSGs para restringir o acesso, em seguida, você precisa certificar-se de que você não quebrar a comunicação que o ASE precisa para operar.  Embora o acesso HTTP/HTTPS é somente por meio do ILB usado do ASE a ASE ainda depende do recurso fora do VNet.  Para ver quais acesso de rede ainda é necessário procure as informações no documento no [Controlando o tráfego de entrada para um ambiente de serviço de aplicativo] [ ControlInbound] e o documento no [Detalhes de configuração de rede para ambientes de serviço de aplicativo com rota expressa][ExpressRoute].  


Para configurar seu NSGs que você precisa saber o endereço IP que é usado pelo Azure para gerenciar seu ASE.  Esse endereço IP também é o endereço IP de saída do seu ASE se isso fizer solicitações de internet.  Para encontrar esse IP endereço vá para **Configurações -> Propriedades** e localizar o **Endereço de IP de saída**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Gerenciamento de geral ILB ASE ####

Gerenciar um ASE ILB é basicamente o mesmo que Gerenciando um ASE normalmente.  Você precisa aumentar os pools de trabalho para hospedar mais ocorrências ASP e dimensionar seus servidores Front-End para lidar com volumes de aumento de tráfego HTTP/HTTPS.  Para obter informações gerais sobre como gerenciar a configuração de um ASE, leia o documento sobre como [Configurar um ambiente de serviço de aplicativo][ASEConfig].  


Os itens de gerenciamento adicionais são gerenciamento de certificados e gerenciamento de DNS.  Você precisa obter e carregar o certificado usado para HTTPS após a criação de ILB ASE e substituí-la antes que ela expire.  Porque o domínio de base é proprietário do Azure podemos fornecer certificados para ASEs com um VIP externos.  Como o subdomínio usado por um ASE ILB pode ser qualquer coisa, você precisa fornecer o seu próprio certificado para HTTPS. 


#### <a name="dns-configuration"></a>Configuração do DNS ####

Ao usar um VIP externos o DNS é gerenciado pelo Azure.  Qualquer aplicativo criado em sua ASE é adicionado automaticamente ao Azure DNS que é um DNS público.  Em um ASE ILB você precisa gerenciar o seu próprio DNS.  Para um determinado subdomínio como contoso.corp.net, você precisa criar DNS A registros que apontem para o seu endereço ILB para:

    * 
    Publicar *.SCM ftp 


## <a name="getting-started"></a>Guia de Introdução
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução aos ambientes de serviço de aplicativo][WhatisASE]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
