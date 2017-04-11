<properties
    pageTitle="Criar um aplicativo web em um ambiente de serviço de aplicativo"
    description="Aprenda a criar aplicativos web e aplicativo planos de serviço em um ambiente de serviço de aplicativo"
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
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Criar um aplicativo web em um ambiente de serviço de aplicativo

## <a name="overview"></a>Visão geral

Este tutorial mostra como criar aplicativos web e planos de serviço de aplicativos em um [Ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md) (ASE). 

> [AZURE.NOTE] Se você deseja saber como criar um aplicativo web, mas não é necessário fazer isso em um ambiente de serviço de aplicativo, consulte [criar um aplicativo da web de .NET](web-sites-dotnet-get-started.md) ou um dos tutoriais relacionados para outros idiomas e estruturas.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você tenha criado um ambiente de serviço de aplicativo. Se você ainda não tiver feito isso, consulte [criar um ambiente de serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar um aplicativo web

1. No [Portal do Azure](https://portal.azure.com/), clique em **Novo > Web + Mobile > Web App**. 

    ![][1]

2. Selecione sua assinatura.  

    Se você tiver várias assinaturas Lembre-se de que, para criar um aplicativo em seu ambiente de serviço de aplicativo, você precisa usar a mesma assinatura que você usou ao criar o ambiente. 

3. Selecione ou crie um grupo de recursos.

    *Grupos de recursos* permitem gerenciar recursos Azure relacionados como uma unidade e são úteis quando estabelecer regras de *controle de acesso baseado em função* (RBAC) para seus aplicativos. Para obter mais informações, consulte [gerenciar seus recursos Azure][ResourceGroups]. 

4. Selecione ou crie um plano de serviço de aplicativo.

    *Planos de serviço de aplicativo* são gerenciadas conjuntos de aplicativos web.  Normalmente quando você selecionar preços, o preço cobrado é aplicado ao plano de serviço de aplicativo em vez de para os aplicativos individuais. Em um ASE você pagar as instâncias de computação alocadas para do ASE em vez do que você tenha listado com o ASP.  Para dimensionar o número de instâncias de um aplicativo web dimensionar as instâncias do seu serviço de aplicativo plano e afeta todos os dos aplicativos da web no plano.  Alguns recursos como slots de site ou integração de VNET também têm restrições de quantidade no plano.  Para obter mais informações, consulte [Visão geral de planos de serviço de aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    Você pode identificar os planos do serviço de aplicativo em seu ASE examinando o local que é observado em nome do plano.  

    ![][5]

    Se você quiser usar um plano de serviço de aplicativo que já existe no seu ambiente de serviço de aplicativo, selecione plano. Se você quiser criar um novo plano de serviço de aplicativo, consulte a seção seguinte deste tutorial, [criar um plano de serviço de aplicativos em um ambiente de serviço de aplicativo](#createplan).

5. Insira o nome de seu aplicativo web e, em seguida, clique em **criar**. 

    Se seu ASE usa um VIP externo a URL de um aplicativo em um ASE é: [*nome do site*]. [*nome do seu ambiente de serviço de aplicativo*]. p.azurewebsites.net em vez de [*nome do site*]. azurewebsites.net
    
    Se seu ASE usar um VIP interno, então a URL de um aplicativo é ASE: [*nome do site*]. [*subdomínio especificado durante a criação de ASE*]   
    Após selecionar o ASP durante a criação de ASE que você verá o subdomínio atualização abaixo do **nome**

## <a name="createplan"></a>Criar um plano de serviço de aplicativo

Quando você cria um plano de serviço de aplicativos em um ambiente de serviço de aplicativo, suas escolhas de trabalho são diferentes, como não há nenhuma trabalhadores compartilhados em uma ASE.  Os operadores que você precisa usar são aquelas que foram alocadas para do ASE por um administrador.  Isso significa que, para criar um novo plano, você precisa ter mais trabalhadores alocados para o pool de trabalhador ASE que o número total de instâncias em todos os seus planos já nesse pool trabalhador.  Se você não tiver o suficiente trabalhadores em seu pool de trabalhador ASE para criar o seu plano, você precisa trabalhar com seu administrador de ASE obtê-los adicionado.

Outra diferença com planos de serviços de aplicativo hospedadas por um ambiente de serviço de aplicativo é a falta de preços seleção.  Quando você tem um ambiente de serviço de aplicativo você está pagando para recursos de computação usados pelo sistema e não tiver adicionados encargos para os planos nesse ambiente.  Normalmente quando você cria um plano de serviço de aplicativo que você selecione um plano de preços que determina a cobrança.  Um ambiente de serviço de aplicativo é essencialmente um local particular onde você pode criar conteúdo.  Você paga para o ambiente e não para hospedar seu conteúdo.

As instruções a seguir mostram como criar um plano de serviço de aplicativo enquanto você estiver criando um aplicativo web, como explicado na seção anterior do tutorial.

1. Clique em **Criar novo** na seleção plano UI e forneça um nome para seu plano como faria normalmente fora um ASE.

2. Selecione o ASE que você deseja usar de seu seletor de local.

    Como um ambiente de serviço de aplicativo é essencialmente um local de implantação particular, ela mostra em local. 

    ![][2]

    Após a seleção de um ASE no seletor de local, a criação de plano de serviço de aplicativo UI atualizações.  O local agora mostra o nome do sistema ASE e a região é em e o seletor de plano de preços é substituído por um seletor de pool de trabalho.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionando um pool de trabalho

Normalmente em um serviço de aplicativo do Azure e fora de um ambiente de serviço de aplicativo, existem 3 tamanhos de computação que estão disponíveis com a seleção de um plano de preço dedicado.  De maneira semelhante, para um ASE você pode definir até 3 pools de trabalhadores e especifique o tamanho de computação que é usado para esse pool de trabalho.  O que isso significa para locatários do ASE é que, em vez de selecionar um plano de preços com tamanho de computação para seu plano de serviço de aplicativo, selecionar o que é chamado de um *pool de trabalho*.  

A seleção de pool de trabalhador UI mostra o tamanho de computação usado para esse pool de trabalho abaixo do nome.  A quantidade disponível se refere a quantas instâncias de computação estão disponíveis para uso no pool.  O pool de total poderá ter mais instâncias que esse número, mas este valor se referir a simplesmente quantos não estão em uso.  Se você precisar ajustar seu ambiente de serviço de aplicativo para adicionar mais recursos de computação consulte [Configurar o ambiente de serviço de aplicativo](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo você ver apenas dois pools de trabalho disponíveis. Isso ocorre porque o administrador ASE alocada apenas hosts em pools essas duas trabalhador.  A terceira aparecerão quando há VMs alocadas para ele.  

## <a name="after-web-app-creation"></a>Após a criação do aplicativo web

Há algumas considerações para execução de aplicativos web e gerenciar planos de serviço de aplicativos em um ASE que precisam ser levadas em conta.  

Conforme mencionado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e como resultado também são responsáveis por garantir que não há capacidade suficiente para hospedar os planos do serviço de aplicativo desejados. Se não houver nenhuma trabalhadores disponíveis, não será capaz de criar o seu plano de serviço de aplicativo.  Isso também é true para dimensionamento de seu aplicativo web.  Se você precisar de mais instâncias você teria obter seu administrador do ambiente de aplicativo de serviço para adicionar mais trabalhadores.

Depois de criar o seu aplicativo web e o plano de serviço de aplicativo é uma boa ideia para dimensioná-la para cima.  Em um ASE você sempre precisa ter pelo menos 2 instâncias do seu plano de serviço de aplicativo para fornecer tolerância para seus aplicativos.  A escala de um plano de serviço de aplicativo em um ASE é a mesma que normal no plano de serviço de aplicativo da interface do usuário.  Para obter mais informações sobre dimensionamento, [como escalar um aplicativo web em um ambiente de serviço de aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
