<properties 
    pageTitle="Como criar um aplicativo Web com o serviço de aplicativo no Linux | Microsoft Azure" 
    description="Web app criação fluxo de trabalho do serviço de aplicativo no Linux." 
    keywords="serviço de aplicativo do Azure, aplicativo web, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="create-a-web-app-with-app-service-on-linux"></a>Criar um aplicativo Web com o serviço de aplicativo no Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>Usando o Portal de gerenciamento para criar seu aplicativo web
Você pode começar a criar seu aplicativo Web no Linux no [portal de gerenciamento](https://portal.azure.com) do, conforme mostrado na imagem abaixo.

![][1]

Depois de selecionar a opção abaixo, você será mostrada a lâmina criar conforme mostrado na imagem abaixo. 

![][2]

-   Dê um nome para seu aplicativo web.
-   Escolha um grupo de recursos existente ou crie um novo. (Consulte regiões disponíveis na [seção limitações](./app-service-linux-intro.md)).
-   Escolha um plano de serviço de aplicativo existente ou crie um novo um (consulte aplicativo serviço plano anotações na [seção limitações](./app-service-linux-intro.md)). 
-   Escolha a pilha de aplicativo que você pretende usar. Você obterá escolher entre várias versões do Node e PHP. 

Quando você tiver o aplicativo criado, você pode alterar a pilha de aplicativo de configurações do aplicativo conforme mostrado na imagem abaixo.

![][3]

## <a name="deploying-your-web-app"></a>Implantando seu aplicativo web

Escolha "opções de implantação" do portal de gerenciamento lhe oferece a opção para usar um repositório de gito ou um repositório GitHub o local para implantar seu aplicativo. As instruções depois são da mesma forma para um aplicativo da web não-Linux e você pode seguir as instruções em nossa [implantação local do gito](./app-service-deploy-local-git.md) ou nosso artigo de [implantação contínua](./app-service-continuous-deployment.md) para GitHub.

Você também pode usar FTP para carregar seu aplicativo para seu site. Você pode obter o ponto de extremidade FTP do seu aplicativo web da seção de logs de diagnóstico conforme mostrado na imagem abaixo.

![][4]


## <a name="next-steps"></a>Próximas etapas ##

* [O que é o serviço de aplicativo no Linux?](./app-service-linux-intro.md)
* [Usar a configuração de PM2 para Node nos aplicativos Web no Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
