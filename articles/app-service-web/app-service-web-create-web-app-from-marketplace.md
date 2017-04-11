<properties
    pageTitle="Criar um aplicativo web do Azure Marketplace | Microsoft Azure"
    description="Aprenda a criar um novo aplicativo web WordPress do Azure Marketplace usando o Portal do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Criar um aplicativo web do Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Do Azure Marketplace disponibiliza uma ampla variedade de aplicativos web populares desenvolvida pela Microsoft, outras empresas e iniciativas de software de fonte aberta. Por exemplo, WordPress, Umbraco CMS, Drupal, etc. Esses aplicativos web são criados em uma ampla variedade de estruturas populares, como [PHP] neste WordPress exemplo, [.NET], [Node], [Java], e [Python], para mencionar alguns. Para criar um aplicativo web do Azure Marketplace o único software que você precisa é o navegador que você usa para o [Portal do Azure].

Neste tutorial, você vai aprender como:

* Encontre e criar web app em um serviço de aplicativo do Azure com base em um modelo do Azure Marketplace.
* Defina configurações de serviço de aplicativo do Azure para o novo aplicativo web.
* Iniciar e gerenciar seu aplicativo web.

Efeitos neste tutorial, você irá implantar um site de blog WordPress do Azure Marketplace. Quando você concluir as etapas deste tutorial, você terá seu próprio site WordPress para cima e em execução na nuvem.

![Painel de aplicativo do exemplo WordPress wep][WordPressDashboard1]

O site de WordPress que você vai implantar neste tutorial usa MySQL para o banco de dados. Se você deseja usar em vez disso, o banco de dados SQL do banco de dados, consulte [Nami de projeto], que também está disponível através do Azure Marketplace.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisará de uma conta do Microsoft Azure. Se você não tiver uma conta, você pode [ativar seus benefícios de assinante do Visual Studio] [ activate] ou [inscrever-se para uma avaliação gratuita][free trial].
>
> Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo]. A partir daí, você pode imediatamente criar um aplicativo da web de curta duração starter no serviço de aplicativo — sem cartão de crédito é necessário e não há nenhum compromissos.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Localizar e criar um aplicativo Web no serviço de aplicativo do Azure

1. Faça logon [Portal do Azure].

1. Clique em **novo**.
    
    ![Criar um novo recurso Azure][MarketplaceStart]
    
1. Procurar **WordPress**e clique em **WordPress**. (Se desejar usar o banco de dados SQL em vez de MySQL, procure **Nami do projeto**.)

    ![Procurar WordPress no mercado][MarketplaceSearch]
    
1. Após ler a descrição do aplicativo WordPress, clique em **criar**.

    ![Criar WordPress web app][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Definir configurações de serviço de aplicativo Azure para seu novo aplicativo Web

1. Após ter criado um novo aplicativo web, a lâmina de configurações de WordPress será exibida, que você usará para concluir as etapas a seguir:

    ![Configurar definições de aplicativo web WordPress][ConfigStart]

1. Insira um nome para o aplicativo da web na caixa **Web app** .

    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo web será *{name}*. azurewebsites.net. Se o nome inserido não exclusivo, um ponto de exclamação vermelho aparece na caixa de texto.

    ![Configurar o nome do aplicativo web WordPress][ConfigAppName]

1. Se você tiver mais de uma assinatura, escolha o que você deseja usar. 

    ![Configurar a inscrição para o aplicativo web][ConfigSubscription]

1. Selecione um **Grupo de recursos** ou crie um novo.

    Para obter mais informações sobre os grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure][ResourceGroups].

    ![Configurar o grupo de recursos para o aplicativo web][ConfigResourceGroup]

1. Selecione um **Local da plano de serviço de aplicativo** ou crie um novo.

    Para obter mais informações sobre planos de serviço de aplicativo, consulte [Visão geral de planos de serviço de aplicativo do Azure][AzureAppServicePlans]. 

    ![Configurar o plano de serviço para o aplicativo web][ConfigServicePlan]

1. Clique em **banco de dados**e, em seguida, na lâmina **Novo banco de dados MySQL** fornecem os valores necessários para configurar seu banco de dados MySQL.

    a. Insira um novo nome ou deixe o nome padrão.

    b. Deixe o **Tipo de banco de dados** definido como **compartilhado**.

    c. Escolha no mesmo local do que você escolheu para o aplicativo web.

    d. Escolha um nível de preços. **Mercúrio** - que é gratuito com espaço em disco e conexões mínimas - está corretamente configurado para este tutorial.

    e. Na lâmina **Novo banco de dados MySQL** , aceite os termos legais e clique em **Okey**. 

    ![Definir as configurações de banco de dados para o aplicativo web][ConfigDatabase]

1. Na lâmina **WordPress** , aceite os termos legais e, em seguida, clique em **criar**. 

    ![Conclua as configurações de aplicativo da web e clique em Okey][ConfigFinished]

    Serviço de aplicativo do Azure cria o aplicativo da web, normalmente em menos de um minuto. Você pode assistir o andamento clicando no ícone de sino na parte superior da página do portal.

    ![Indicador de progresso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerenciar seu aplicativo web do WordPress
    
1. Quando terminar a criação de aplicativos web, navegue no Portal do Azure para o grupo de recursos em que você criou o aplicativo e você pode ver o aplicativo da web e o banco de dados.

    O recurso adicional com o ícone de lâmpada está [Ideias de aplicativo][ApplicationInsights], que fornece serviços de monitoramento para seu aplicativo web.

1. Na lâmina **grupo de recursos** , clique na linha de aplicativo web.

    ![Selecione o aplicativo web do WordPress][WordPressSelect]

1. Na lâmina Web app, clique em **Procurar**.

    ![Navegue até o aplicativo web do WordPress][WordPressBrowse]

1. Se você for solicitado para selecionar o idioma para o seu blog WordPress, selecione o idioma desejado e clique em **continuar**.

    ![Configurar o idioma para o aplicativo da web de WordPress][WordPressLanguage]

1. Na página **Bem-vindo** WordPress, insira as informações de configuração necessárias por WordPress e clique em **Instalar WordPress**.

    ![Definir as configurações de seu aplicativo web do WordPress][WordPressConfigure]

1. Faça logon usando as credenciais que você criou na página de **boas-vindas** .  

1. A página de painel do site será aberto e exibir as informações que você forneceu.    

    ![Exibir seu painel WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como criar e implantar um aplicativo da web de exemplo do Azure Marketplace.

Para obter mais informações sobre como trabalhar com o aplicativo de serviço Web Apps, consulte os links no lado esquerdo da página (para windows de navegador ampla) ou na parte superior da página (para windows de navegador estreito).

Para obter mais informações sobre como desenvolver aplicativos da web de WordPress no Azure, consulte [Desenvolvendo WordPress no serviço de aplicativo do Azure][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Experimente o aplicativo de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal do Azure]: https://portal.azure.com/
[Nami do projeto]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
