<properties 
    pageTitle="Depurar um aplicativo da Web de Java no Azure em IntelliJ | Microsoft Azure" 
    description="Este tutorial mostra como usar o Kit de ferramentas do Azure para IntelliJ para depurar um aplicativo da Web de Java em execução no Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Depurar um aplicativo da Web de Java no Azure no IntelliJ

Este tutorial mostra como depurar um aplicativo da Web de Java em execução no Azure usando o [Kit de ferramentas do Azure para IntelliJ]. Para simplificar, você usará um exemplo de página de servidor de Java (JSP) básico para este tutorial, mas as etapas seriam semelhantes para um servlet Java ao depurar no Azure.

Quando você concluir este tutorial, seu aplicativo terá aparência semelhante à ilustração a seguir ao depurá-la no IntelliJ:

![][01]
 
## <a name="prerequisites"></a>Pré-requisitos

* Um Java Developer Kit (JDK), v 1,8 ou posterior.
* IntelliJ IDEIA Ultimate Edition. Isso pode ser baixado do <https://www.jetbrains.com/idea/download/index.html>.
* Uma distribuição de um servidor de web baseados em Java ou o servidor de aplicativos, como o Apache Tomcat ou Jetty.
* Uma assinatura do Azure, que pode ser adquirida no <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de ferramentas Azure para IntelliJ. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para IntelliJ].
* Um projeto de Web dinâmico criadas e implantadas ao serviço de aplicativo do Azure; Por exemplo, consulte [criar um Hello World Web App para Azure no IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Depurar um aplicativo da Web de Java no Azure

Para concluir essas etapas desta seção, você pode usar um projeto de Web dinâmico existente que você já implantou como um aplicativo da Web de Java no Azure, você baixar um [Projeto de Web dinâmico de amostra] e siga as etapas em [criar um Hello World Web App para Azure no IntelliJ] implantá-lo no Azure. 

1. Abra o projeto Java Web App que você distribuiu para Azure no IntelliJ.

1. Clique no menu **Executar** e clique em **Editar configurações**.

    ![][02]

1. Quando abre a caixa de diálogo **Configurações de executar/depurar** : 

    1. Selecione **Azure Web App**.
    1. Clique em **+** para adicionar uma nova configuração.
    1. Forneça um **nome** para a configuração.
    1. Aceite os valores padrão restantes que são sugeridos pelo Kit de ferramentas do Azure e clique em **Okey**.

        ![][03]

1. Selecione a configuração de depuração do Azure Web App que você acabou de criar no canto superior direito do menu e clique em **Depurar**

    ![][04]

1. Quando solicitado a **Ativar depuração remota no aplicativo Web remoto agora?**, clique em **Okey**.

1. Quando solicitado a **seu aplicativo web agora está pronto para depuração remota**, clique em **Okey**.

    ![][05]

1. Selecione a configuração de depuração do Azure Web App que você acabou de criar no canto superior direito do menu e clique em **Depurar**.

1. Um prompt de comando do Windows ou shell do Unix será aberto e preparar conexão necessária para depuração; Você precisa esperar até que a conexão para o seu aplicativo Web Java remoto for bem-sucedida antes de continuar. Se você estiver usando o Windows, ele será parecida com a ilustração a seguir:

    ![][06]

1. Inserir um ponto de interrupção na sua página JSP e, em seguida, abra a URL para o aplicativo da Web de Java em um navegador:

    1. Abrir o **Azure Explorer** em IntelliJ.
    1. Navegue para **Aplicativos Web** e o aplicativo da Web de Java que você deseja depurar.
    1. Clique com o botão direito do aplicativo Web e clique em **Abrir no navegador**.
    1. IntelliJ agora entrará no modo de depuração.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

Para obter informações adicionais sobre a criação de aplicativos Web do Azure, consulte [Visão geral de aplicativos Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ferramentas Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Instalando o Kit de ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Exemplo de projeto de dinâmico da Web]: http://go.microsoft.com/fwlink/?LinkId=817337

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/
[Visão geral de aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
