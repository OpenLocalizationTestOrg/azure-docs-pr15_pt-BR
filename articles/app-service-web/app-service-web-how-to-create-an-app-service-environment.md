<properties 
    pageTitle="Como criar um ambiente de serviço de aplicativo" 
    description="Descrição de fluxo de criação de ambientes de serviço de aplicativo" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Como criar um ambiente de serviço de aplicativo #

### <a name="overview"></a>Visão geral ###

Ambientes de serviço de aplicativo (ASE) são uma opção de serviço Premium de serviço de aplicativo do Azure que fornece um recurso de configuração avançada que não está disponível nos carimbos de vários locatários.  O recurso de ASE essencialmente implanta o serviço de aplicativo do Azure na rede virtual de um cliente.  Para obter uma compreensão maior dos recursos oferecidos pelo ambientes de serviço de aplicativo ler o [o que é um ambiente de serviço de aplicativo] [ WhatisASE] documentação.

### <a name="before-you-create-your-ase"></a>Antes de criar sua ASE ###

É importante Lembre-se das coisas que você não pode alterar.  Esses aspectos que você não pode alterar sobre sua ASE após sua criação são:

- Local
- Assinatura
- Grupo de recursos
- VNet usado
- Sub-rede usada 
- Tamanho de sub-rede

Quando um VNet de separação e especificando uma sub-rede, verifique se é grande o suficiente para acomodar qualquer futuro crescimento.  

### <a name="creating-an-app-service-environment"></a>Criando um ambiente de serviço de aplicativo ###

Há duas maneiras de acessar a criação de ASE interface do usuário.  Pode ser encontrado pesquisando do Azure Marketplace para ***Ambiente de serviço de aplicativo*** ou através do novo -> Web + Mobile -> ambiente de serviço de aplicativo.  Para criar um ASE:

1. Fornece o nome de seu ASE.  O nome que está especificado para o ASE será usado para os aplicativos criados do ASE.  Se o nome do ASE é appsvcenvdemo o nome de subdomínio seria. *appsvcenvdemo.p.azurewebsites.net*.  Se você criou, portanto, um aplicativo chamado *mytestapp* seria endereçamento em *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  Você não pode usar o espaço em branco na nome do seu ASE.  Se você usar caracteres em letras maiusculas no nome, o nome de domínio será a versão de letras minúsculas total desse nome.  Se você usar um ILB, em seguida, o nome do seu ASE não é usado em um subdomínio, mas em vez disso, é explicitamente declarado durante a criação de ASE

    ![][1]

2. Selecione sua assinatura.  A assinatura usada para sua ASE também é aquele que todos os aplicativos no que ASE serão criados com.  Você pode colocar seu ASE em uma VNet que está em outra assinatura

3. Selecione ou especifique um novo grupo de recursos.  O grupo de recursos usado para sua ASE deve ser o mesmo usado para sua VNet.  Se você selecionar uma VNet preexistente, em seguida, a seleção de grupo de recursos para sua ASE será atualizada para refletir que sua VNet.

    ![][2]

4. Faça suas seleções de rede Virtual e um local.  Você pode optar por criar um novo VNet ou selecionar um VNet preexistente.  Se você selecionar um novo VNet você pode especificar um nome e local. O novo VNet terá o endereço intervalo 192.268.250.0/23 e uma sub-rede nomeados **padrão** que é definido como 192.168.250.0/24.  Você pode também simplesmente selecionar um clássico preexistente ou VNet do Gerenciador de recursos.  A seleção de tipo de VIP determina se seu ASE pode ser acessado diretamente da internet (externo) ou se ele usa um balanceador de carga interno (ILB).  Para saber mais sobre eles lidos [usando um balanceador de carga interno com um ambiente de serviço de aplicativo][ILBASE].  Se você selecionar um tipo de VIP de externos, em seguida, você pode selecionar quantos endereços IP externos, o sistema é criado com para fins IPSSL.  Se você selecionar interno, em seguida, você precisa especificar o subdomínio que seu ASE usará.  ASEs podem ser implantados em redes virtuais que usam *um dos* intervalos de endereços público, espaços de endereço *ou* RFC1918 (ou seja endereços privados).  Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar o VNet antecedência.  Quando você seleciona uma VNet preexistente que você precisa criar uma nova sub-rede durante a criação de ASE.  **Você não pode usar uma sub-rede pré-criada no portal.  Você pode criar um ASE com uma sub-rede preexistente se você criar seu ASE usando um modelo do Gerenciador de recursos.**  Para criar um ASE de um modelo, use as informações aqui, [Criando um ambiente de serviço de aplicativo de modelo] [ ILBAseTemplate] e aqui, [Criando um ambiente de serviço de aplicativo de ILB de modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes ###

Um ASE é criado com 2 Front-Ends e 2 trabalhadores.  O Front-Ends atuar como os pontos de extremidade HTTP/HTTPS e enviar tráfego para os funcionários que são as funções que hospedam seus aplicativos.   Você pode ajustar a quantidade após a criação de ASE e pode até mesmo configurar regras de escala automática nesses pools de recursos.  Para obter mais detalhes em torno de redimensionamento manual, gerenciamento e monitoramento de um ambiente de serviço de aplicativo ir aqui: [como configurar um ambiente de serviço de aplicativo][ASEConfig] 

Somente a um ASE pode existir na sub-rede usada do ASE.  A sub-rede não pode ser usada para qualquer coisa diferente do ASE

### <a name="after-app-service-environment-creation"></a>Após a criação do ambiente de serviço de aplicativo ###

Após a criação de ASE, você pode ajustar:

- Quantidade de Front-Ends (mínimo: 2)
- Quantidade de trabalhadores (mínimo: 2)
- Quantidade de endereços IP disponíveis para IP SSL
- Calcular tamanhos de recurso usados pelo Front-Ends ou trabalhadores (tamanho mínimo de Front-End é P2)


Há mais detalhes em torno de dimensionamento, gerenciamento manual e monitoramento de ambientes de serviço de aplicativo aqui: [como configurar um ambiente de serviço de aplicativo][ASEConfig] 

Para obter informações sobre autoscaling há um guia aqui: [como configurar escala automática para um ambiente de serviço de aplicativo][ASEAutoscale]

Há dependências adicionais que não estão disponíveis para personalização como o armazenamento e o banco de dados.  Estas são tratadas por Azure e vêm com o sistema.  O armazenamento de sistema suporta até 500 GB para todo o ambiente de serviço do aplicativo e o banco de dados será ajustado por Azure como necessário a escala do sistema.


## <a name="getting-started"></a>Guia de Introdução
Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução aos ambientes de serviço de aplicativo][WhatisASE]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
