<properties 
    pageTitle="Depurar um aplicativo da Web de Java no Azure no Eclipse | Microsoft Azure" 
    description="Este tutorial mostra como usar o Kit de ferramentas do Azure para Eclipse para depurar um aplicativo da Web de Java em execução no Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Depurar um aplicativo da Web de Java no Azure no Eclipse

Este tutorial mostra como depurar um aplicativo da Web de Java em execução no Azure usando o [Kit de ferramentas do Azure para Eclipse]. Para simplificar, você usará um exemplo de página de servidor de Java (JSP) básico para este tutorial, mas as etapas seriam semelhantes para um servlet Java ao depurar no Azure.

Quando você concluir este tutorial, seu aplicativo terá aparência semelhante a ilustração a seguir ao depurá-lo no Eclipse:

![][01]
 
## <a name="prerequisites"></a>Pré-requisitos

* Um Java Developer Kit (JDK), v 1,8 ou posterior.
* Eclipse IDE para Java EE desenvolvedores, Indigo ou posterior. Isso pode ser baixado do <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor de web baseados em Java ou o servidor de aplicativos, como o Apache Tomcat ou Jetty.
* Uma assinatura do Azure, que pode ser adquirida no <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de ferramentas Azure para Eclipse. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para Eclipse].
* Um projeto de Web dinâmico criadas e implantadas ao serviço de aplicativo do Azure; Por exemplo, consulte [criar um Hello World Web App para Azure no Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Depurar um aplicativo da Web de Java no Azure

Para concluir essas etapas desta seção, você pode usar um projeto de Web dinâmico existente que você já implantou como um aplicativo da Web de Java no Azure, você baixar um [Projeto de Web dinâmico de amostra] e siga as etapas em [criar um Hello World Web App para Azure no Eclipse] implantá-lo no Azure. 

1. Abra o Eclipse.

1. Configure tempos limite para depuração remota:

    1. Clique no menu do **Windows** do Eclipse e clique em **Preferências**.
    1. Expanda o nó **Java** e selecione **Depurar**.
    1. Definir configurações de **tempo limite do depurador (ms)** tanto o **Iniciar tempo limite (ms)** para `120000`.

        ![][02]

    1. Clique **Okey** para fechar a caixa de diálogo **Preferências** .

1. No modo de exibição do Eclipse Explorador de projeto, botão direito clique em projeto da Web dinâmico que você implantou no Azure. Quando aparece o menu de contexto, selecione **Depurar como**e, em seguida, clique em **Azure Web App**.

    ![][03]

1. Se esta for a primeira vez que depuração de seu projeto Web dinâmico, caixa de diálogo **Configurações de depuração** será aberta; Você pode aceitar os valores padrão que são especificados pelo Kit de ferramentas na guia **Connect** . Na guia **fonte** , clique em **Adicionar**e, em seguida, **projeto Java**, selecione **Dinâmico projeto da Web**e clique em **Okey**. Depois de concluir essas etapas, clique em **Depurar**.

    ![][04]

1. Quando solicitado a **Ativar depuração remota no aplicativo Web remoto agora?**, clique em **Okey**.

1. Quando solicitado a **seu aplicativo web agora está pronto para depuração remota**, clique em **Okey**.

    ![][05]

1. Quando a caixa de diálogo **Configurações de depurar** reaparece, clique em **Depurar**.

1. Um prompt de comando do Windows ou shell do Unix será aberto e preparar conexão necessária para depuração; Você precisa esperar até que a conexão para o seu aplicativo Web Java remoto for bem-sucedida antes de continuar. Se você estiver usando o Windows, ele será aparência a ilustração a seguir.

    ![][06]

1. Inserir um ponto de interrupção na sua página JSP e, em seguida, abra a URL para o aplicativo da Web de Java em um navegador:

    1. Abrir o **Explorer Azure** no Eclipse.
    1. Navegue para **Aplicativos Web** e o aplicativo da Web de Java que você deseja depurar.
    1. Clique com o botão direito do aplicativo Web e clique em **Abrir no navegador**.
    1. Agora, o Eclipse entrará no modo de depuração.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

Para obter informações adicionais sobre a criação de aplicativos Web do Azure, consulte [Visão geral de aplicativos Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ferramentas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Instalando o Kit de ferramentas do Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Exemplo de projeto de dinâmico da Web]: http://go.microsoft.com/fwlink/?LinkId=817337

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/
[Visão geral de aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
