<properties 
    pageTitle="Adicionar um aplicativo Java para aplicativos do Azure aplicativo de serviço Web" 
    description="Este tutorial mostra como adicionar uma página ou o aplicativo para sua instância do Azure aplicativo de serviço Web Apps que já está configurado para usar Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Adicionar um aplicativo Java para aplicativos do Azure aplicativo de serviço Web

Depois que você tiver inicializado seu aplicativo web do Java no [Serviço de aplicativo do Azure][] como documentadas ao [criar um aplicativo da web de Java no serviço de aplicativo do Azure](web-sites-java-get-started.md), você pode carregar seu aplicativo colocando seu guerra na pasta **webapps** .

O caminho de navegação para a pasta **webapps** difere com base em como configurar sua instância de aplicativos Web.

- Se você configurar o seu aplicativo web usando o Azure Marketplace, o caminho para a pasta **webapps** é no formulário **d:\home\site\wwwroot\bin\application\_server\webapps**, onde **aplicativo\_servidor** é o nome do servidor do aplicativo em vigor para sua instância de aplicativos Web. 
- Se você configurar o seu aplicativo web usando a configuração Azure interface do usuário, o caminho para a pasta **webapps** é no formulário **d:\home\site\wwwroot\webapps**. 

Observe que você pode usar o controle de origem para carregar seu aplicativo ou páginas da web, incluindo [cenários de integração contínua](app-service-continuous-deployment.md). FTP também é uma opção para carregar seu aplicativo ou páginas da web.

Observação para Tomcat web apps: depois de carregar seu arquivo de guerra à pasta **webapps** , o servidor de aplicativo Tomcat detecta que você adicionou-lo e automaticamente carregá-lo. Observe que, se você copiar arquivos (diferente de arquivos de guerra) para o diretório raiz, o servidor de aplicativo precisam ser reiniciado antes desses arquivos são usados. A funcionalidade de carregamento automático para aplicativos da web Tomcat Java em execução no Azure baseia-se em um novo arquivo de guerra sendo adicionado, ou novos arquivos ou pastas adicionadas à pasta **webapps** . 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Java Developer Center](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Serviço de aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
