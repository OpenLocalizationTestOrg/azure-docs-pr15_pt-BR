<properties
    pageTitle="Visão geral detalhada de planos de serviço de aplicativo do Azure | Microsoft Azure"
    description="Saiba como os planos de serviço de aplicativo para o trabalho de serviço de aplicativo do Azure e como eles trazem benefícios à sua experiência de gerenciamento."
    keywords="aplicativo, azure aplicativo serviço, escaláveis, plano de serviço de aplicativo escala, custos de serviço de aplicativo"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Visão geral detalhada de planos de serviço de aplicativo do Azure#

Um plano de serviço de aplicativo representa um conjunto de recursos e a capacidade que você pode compartilhar entre vários aplicativos. Aplicativos Web, aplicativos Mobile, função aplicativos ou aplicativos de API, no [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) todos executar em um plano de serviço de aplicativo. Esses planos cinco níveis de preços de suporte: *livre*, *compartilhado*, *básica*, *Standard*e *Premium*. Cada nível tem seu próprio recursos e a capacidade. Aplicativos na mesma assinatura e localização geográfica podem compartilhar um plano. Todos os aplicativos que compartilham um plano podem usar todos os recursos e os recursos que são definidos por nível do plano. Todos os aplicativos que estão associados um plano executados nos recursos que define o plano.

Por exemplo, se seu plano estiver configurado para usar duas instâncias de "pequenas" no nível de serviço padrão, todos os aplicativos que estão associados esse plano executado em ambas as instâncias e tem acesso a funcionalidade de nível de serviço padrão. Instâncias de plano no qual estão executando aplicativos são totalmente gerenciado e altamente disponível.

Este artigo aborda as características principais, como camada e escala, de um plano de serviço de aplicativo e como eles entram durante o gerenciamento de seus aplicativos.

## <a name="apps-and-app-service-plans"></a>Aplicativos e planos de serviço de aplicativo

Um aplicativo no serviço de aplicativo pode ser associado a apenas um plano de serviço de aplicativo a qualquer momento.

Aplicativos e planos estão contidos em um grupo de recursos. Um grupo de recursos serve como o limite de ciclo de vida para cada recurso que está dentro dela. Você pode usar grupos de recursos para gerenciar todas as partes de um aplicativo juntos.

Como um grupo único recurso pode ter vários planos de serviço de aplicativo, você pode alocar diferentes aplicativos para recursos físicos diferentes. Por exemplo, você pode separar recursos entre ambientes de desenvolvimento, teste e produção. Ter ambientes separados para desenvolvimento/teste e produção permite isolar recursos. Dessa maneira, teste em relação a uma nova versão de seus aplicativos de carga não competir para os mesmos recursos como seus aplicativos de produção, que são servindo clientes reais.

Quando você tiver vários planos em um grupo único recurso, você também pode definir um aplicativo que abranja regiões geográficas. Por exemplo, um aplicativo altamente disponível executado em duas regiões inclui pelo menos dois planos, uma para cada região e um aplicativo associada a cada plano. Em uma situação, todas as cópias do aplicativo, em seguida, estão contidas em um grupo de recursos único. Ter um grupo de recursos com vários planos e vários aplicativos facilita a gerenciar, controlar e exibir a integridade do aplicativo.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Criar um plano de serviço de aplicativo ou use um existente

Quando você cria um aplicativo, você deve considerar a criação de um grupo de recursos. Por outro lado, se o aplicativo que você está prestes a criar um componente para um aplicativo maior, este aplicativo deve ser criado no grupo de recursos que está alocado para esse aplicativo maior.

Se o novo aplicativo é um aplicativo totalmente novo ou parte de uma maior, você pode optar por usar um plano de serviço de aplicativo existente para hospedá-lo ou criar um novo. Essa decisão é mais uma pergunta de capacidade e carga esperada.

Se este novo aplicativo vai usar muitos recursos e tem diferentes fatores de outros aplicativos de dimensionamento hospedado em um plano existente, recomendamos que você isolá-las no seu próprio plano.

Quando você cria um plano, você pode alocar um novo conjunto de recursos para seu aplicativo e obter um controle maior sobre alocação de recursos, porque cada plano obtém seu próprio conjunto de instâncias.

Porque você pode mover aplicativos entre os planos, você pode alterar a maneira que recursos estão alocados todo o aplicativo maior.

Finalmente, se você deseja criar um aplicativo em uma região diferente e região não tem um plano existente, crie um plano naquela região sejam capazes de hospedar seu aplicativo lá.

## <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicativo

>[AZURE.TIP] Se você tiver um ambiente de serviço de aplicativo, você pode examinar a documentação específica para ambientes de serviço de aplicativo aqui: [criar um plano de serviço de aplicativo em um ambiente de serviço de aplicativo](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Você pode criar um plano de serviço de aplicativo vazio com a experiência de procurar de plano de serviço de aplicativo ou como parte de criação do aplicativo.

No [portal do Azure](https://portal.azure.com), clique em **novo** > **Web + mobile**e em seguida, selecione **Web App** ou outro tipo de aplicativo de serviço de aplicativo.
![Crie um aplicativo no portal do Azure.][createWebApp]

Você pode selecionar ou criar o plano de serviço de aplicativo para o novo aplicativo.

 ![Crie um plano de serviço de aplicativo.][createASP]

Para criar um novo plano de serviço de aplicativo, clique em **[+] Criar nova**, digite o nome do **plano de serviço de aplicativo** e, em seguida, selecione um **local**de apropriado. Clique em **nível de preços**e selecione um nível de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preços, como **livre** e **compartilhado**. Depois que você selecionou a camada de preços, clique no botão **Selecionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover um aplicativo para um plano diferente do serviço de aplicativo

Você pode mover um aplicativo para um plano de serviço de aplicativo diferente no [portal do Azure](https://portal.azure.com). Você pode mover aplicativos entre planos desde os planos são no mesmo grupo de recursos e área geográfica.

Para mover um aplicativo para outro plano, vá para o aplicativo que você deseja mover. No menu **configurações** , procure o **Plano de serviço de aplicativo de alteração**.

**Plano de serviço de aplicativo de alteração** abre o seletor de **plano de serviço de aplicativo** . Neste ponto, você pode escolher um plano existente ou crie um novo. Mostra somente os planos válidos (no mesmo grupo de recursos e localização geográfica).

![Seletor de plano de serviço de aplicativo.][change]

Cada plano tem seu próprio nível de preços. Por exemplo, quando você move um site de um nível gratuito para um nível padrão, seu aplicativo agora pode usar todos os recursos e os recursos da camada padrão.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar um aplicativo para um plano diferente do serviço de aplicativo
Se você deseja mover o aplicativo para uma região diferente, uma alternativa é aplicativo clonagem. Clonagem faz uma cópia de seu aplicativo em um ambiente de serviço de aplicativo em qualquer região ou plano de serviço de aplicativo novo ou existente.

 ![Clone um aplicativo.][appclone]

Você pode encontrar **O aplicativo de clonar** no menu **Ferramentas** .

Clonagem tem algumas limitações que você pode ler sobre no [aplicativo de serviço de aplicativo do Azure clonagem usando o portal do Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Escala de um plano de serviço de aplicativo

Há três maneiras de dimensionar um plano:

- **Alterar o plano preços camada**. Por exemplo, um plano na camada básica pode ser convertido em um nível padrão ou Premium e todos os aplicativos que estão associados esse plano agora podem usar os recursos que oferece o novo nível de serviço.
- **Alterar o tamanho da instância do plano**. Como exemplo, um plano na camada básica que usa instâncias pequenas pode ser alterado para usar instâncias grandes. Todos os aplicativos que estão associados esse plano agora podem usar a memória adicional e recursos de CPU que oferece o maior tamanho de instância.
- **Alterar a contagem de instâncias do plano**. Por exemplo, um plano padrão que é dimensionado para três instâncias pode ser dimensionado para 10 instâncias. Um plano de Premium pode ser dimensionado para 20 instâncias (sujeitos a disponibilidade). Todos os aplicativos que estão associados esse plano agora podem usar a memória adicional e recursos de CPU que oferece a maior contagem de instância.

Você pode alterar o tamanho de camada e instância preços clicando no item **Escala para cima** em configurações para o aplicativo ou o plano de serviço de aplicativo. Alterações aplicam ao plano de serviço de aplicativo e afetam todos os aplicativos que hospeda.

 ![Definir valores para dimensionar um aplicativo.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpeza de plano de serviço de aplicativo
**Planos de serviço de aplicativo** que tenho sem aplicativos associados a elas ainda provoca encargos desde que eles continuam a reservar a capacidade de computação configurada nas propriedades de escala de plano de serviço de aplicativo.
Para evitar encargos inesperados, quando o último aplicativo hospedado em um plano de serviço de aplicativo é excluído, o plano de serviço de aplicativo vazio resultante também será excluído.


## <a name="summary"></a>Resumo

Planos de serviço de aplicativo representam um conjunto de recursos e a capacidade que você pode compartilhar em seus aplicativos. Planos de serviço de aplicativo oferecem flexibilidade para alocar aplicativos específicos para um conjunto de recursos e otimizar ainda mais a utilização do recurso Azure. Dessa forma, se você quiser salvar o dinheiro em seu ambiente de teste, você pode compartilhar um plano entre vários aplicativos. Você também pode maximizar produtividade para seu ambiente de produção por dimensioná-lo em várias regiões e planos.

## <a name="whats-changed"></a>O que mudou

* Para um guia para a alteração de sites para o serviço de aplicativo, consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png
