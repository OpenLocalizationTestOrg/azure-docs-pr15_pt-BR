<properties 
    pageTitle="Como dimensionar um aplicativo em um ambiente de serviço de aplicativo" 
    description="Dimensionamento de um aplicativo em um ambiente de serviço de aplicativo" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Dimensionamento de aplicativos em um ambiente de serviço de aplicativo #

No serviço de aplicativo do Azure há normalmente três coisas, que você pode dimensionar:

- plano de preços
- tamanho de trabalho 
- número de instâncias.

Em um ASE não é necessário para selecionar ou alterar o plano de preços.  Em termos de recursos é já alto nível de capacidade de preços.  

Relacionadas com tamanhos de trabalho, o administrador ASE pode atribuir o tamanho do recurso computação a ser usado para cada grupo de trabalho.  Isso significa que você pode ter 1 de Pool de trabalho com recursos de computação P4 e 2 de Pool de trabalho com P1 calcular recursos, se desejar.  Eles não precisam estar em ordem de tamanho.  Para obter detalhes em torno os tamanhos e seu preços ver o documento aqui [Preços de serviço de aplicativo do Azure][AppServicePricing].  Isso deixa as opções de dimensionamento para aplicativos web e planos de serviço de aplicativo em um ambiente de serviço de aplicativo seja:

- seleção de pool de trabalho
- número de instâncias

Alterando o item é feito por meio da UI apropriada mostrada para seu ASE hospedado planos de serviço de aplicativo.  

![][1]

Você não pode aumentar o ASP além do número de recursos de computação disponíveis no pool de trabalho que o ASP está em.  Se você precisar calcular recursos em pool trabalhador você precisa obter o administrador ASE adicioná-las.  Para obter informações em torno de reconfiguração seu ASE leia as informações aqui: [como configurar um ambiente de serviço de aplicativo][HowtoConfigureASE].  Você também pode querer aproveitar os recursos de escala automática ASE para adicionar a capacidade com base no cronograma ou métricas.  Para obter mais detalhes sobre como configurar escala automática para o ASE próprio ambiente consulte [como configurar escala automática para um ambiente de serviço de aplicativo][ASEAutoscale].

Você pode criar aplicativo vários planos de serviço usando recursos de computação de grupos de trabalho diferentes, ou você pode usar o mesmo pool de trabalho.  Recursos de computação por exemplo se você tiver (10) recursos de computação disponíveis em 1 de Pool de trabalho, você pode optar por criar um plano de serviço de aplicativo usando os recursos de computação (6) e planejar um segundo serviço de aplicativo que usa (4).

### <a name="scaling-the-number-of-instances"></a>O número de instâncias de dimensionamento ###

Quando você cria seu aplicativo web em um ambiente de serviço de aplicativo é iniciado com 1 instância.  Em seguida, é possível ampliar para instâncias adicionais para fornecer recursos de computação adicionais para seu aplicativo.   

Se seu ASE tiver capacidade suficiente, em seguida, isso é bem simple.  Você acesse seu plano de serviço de aplicativo que contém os sites que você deseja dimensionar e selecione escala.  Isso abre a interface do usuário onde você pode definir a escala para o ASP ou configurar regras de escala automática para o ASP manualmente.  Para Dimensionar manualmente o seu aplicativo simplesmente defina a ***escala por*** para ***uma contagem de instância que eu insiro manualmente***.  A partir daqui, arraste o controle deslizante para a quantidade desejada ou insira-o na caixa ao lado do controle deslizante.  

![][2] 

As regras de escala automática para um ASP em um ASE funcionam da mesma forma que eles fazem normalmente.  Você pode selecionar ***Porcentagem de CPU*** em ***escala por*** e criar regras de escala automática para o ASP com base na porcentagem de CPU ou você pode criar regras mais complexas usando ***as regras de cronograma e desempenho***.  Para ver mais detalhes completos sobre como configurar a escala automática usar o guia aqui [Dimensionar um aplicativo no serviço de aplicativo do Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Seleção de Pool de trabalho ###

Conforme mencionado anteriormente, a seleção de pool de trabalho é acessada da interface ASP.  Abra a lâmina para o ASP que você deseja dimensionar e selecione pool de trabalho.  Você verá que todos os grupos de trabalho que você configurou no seu ambiente de serviço de aplicativo.  Se você tiver o pool de somente um trabalhador, em seguida, você só verá o um pool listado.  Para alterar qual pool de trabalhador o ASP está em, basta selecionar o pool de trabalho desejada seu plano de serviço de aplicativo para ir para.  

![][3]

Antes de passar o ASP do pool de um funcionário para outro é importante garantir que você terá capacidade adequada para seu ASP.  Na lista de grupos de trabalho, não apenas está listado o nome do pool de trabalho, mas você também pode ver quantos funcionários estão disponíveis em pool trabalhador.  Certifique-se de que há instâncias suficiente disponíveis para conter seu plano de serviço de aplicativo.  Se você precisar calcular mais recursos do pool de trabalho que você deseja mover para, em seguida, obtenha seu administrador ASE para adicioná-los.  

> [AZURE.NOTE] Mover que uma ASP do pool de um funcionário causará resfriado inicia dos aplicativos em que o ASP.  Isso pode causar solicitações lento como seu aplicativo está frio introdução sobre os novos recursos de computação.  Iniciar fria pode ser evitada usando o [aplicativo aquecimento recurso] [ AppWarmup] no serviço de aplicativo do Azure.  O módulo de inicialização do aplicativo descrito no artigo também funciona para inícios frio, porque o processo de inicialização também é invocado quando aplicativos estão frio iniciado em novos recursos de computação. 

## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o aplicativo de serviço ambientes, consulte [Como para criar um aplicativo do ambiente de serviço][HowtoCreateASE]

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
