<properties 
    pageTitle="Criar um aplicativo de Web Olá mundo para Azure em IntelliJ | Microsoft Azure" 
    description="Este tutorial mostra como usar o Kit de ferramentas do Azure para IntelliJ para criar um Hello World Web App para o Azure." 
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
    ms.date="08/11/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>Criar um aplicativo de Web Olá mundo para Azure no IntelliJ

Este tutorial mostra como criar e implantar um aplicativo Hello World básico no Azure como um aplicativo Web usando o [Kit de ferramentas do Azure para IntelliJ]. Um exemplo JSP básico é mostrado para simplicidade, mas etapas altamente semelhantes seria apropriadas para um servlet Java, que diz respeito implantação Azure.

Quando você concluir este tutorial, seu aplicativo terá aparência semelhante a ilustração a seguir quando você vê-lo em um navegador da web:

![][01]
 
## <a name="prerequisites"></a>Pré-requisitos

* Um Java Developer Kit (JDK), v 1,8 ou posterior.
* IntelliJ IDEIA Ultimate Edition. Isso pode ser baixado do <https://www.jetbrains.com/idea/download/index.html>.
* Uma distribuição de um servidor de web baseados em Java ou o servidor de aplicativos, como o Apache Tomcat ou Jetty.
* Uma assinatura do Azure, que pode ser adquirida no <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de ferramentas Azure para IntelliJ. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Para criar um aplicativo do mundo de saudação

Primeiro, começaremos com a criação de um projeto Java.

1. Iniciar IntelliJ, no menu, clique em **arquivo**e em seguida **novo**e, em seguida, clique em **projeto**.

   ![][02]

1. Na caixa de diálogo Novo projeto, selecione **Java**, em seguida, **Aplicativo Web**e clique em **Avançar**.

   ![][03a]

   Se solicitado a continuar com nenhum SDK atribuído, clique em **Sim**.

   ![][03b]

1. Para fins deste tutorial, nomeie o projeto **Java Web App no Azure**e clique em **Concluir**.

   ![][04]

1. No modo de exibição do IntelliJ Explorador de projeto, expanda **Java Web App no Azure**, e em seguida, expanda **web**e clique duas vezes em **JSP**.

   ![][05]

1. Quando o arquivo JSP abre no IntelliJ, adicionar texto para exibir dinamicamente **Olá, mundo!** dentro das existentes `<body>` elemento. Seu atualizado `<body>` conteúdo deve se parecer com o exemplo a seguir:

   `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Salve JSP.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Para implantar seu aplicativo a um contêiner de aplicativo Web do Azure

Existem várias maneiras pelo qual você pode implantar um aplicativo da web de Java no Azure. Este tutorial descreve a mais simples: seu aplicativo será implantado em um contêiner de aplicativo Web do Azure - nenhum tipo de projeto especial nem ferramentas adicionais são necessários. O JDK e o software de contêiner de web serão fornecidos para você por Azure, portanto, não é necessário para carregar seus próprios; tudo que você precisa é seu aplicativo Web do Java. Como resultado, o processo de publicação para seu aplicativo levará segundos, não minutos.

1. No Explorador de projeto do IntelliJ, clique com botão direito do projeto **Java Web App no Azure** . Quando aparece o menu de contexto, selecione **Azure**e clique em **Publicar como Azure Web App....**

   ![][06]

1. Se você não tiver entrado já no Azure IntelliJ, você será solicitado a entrar em sua conta do Azure:

   ![][07]

   Observação: Se você tiver várias contas do Azure, alguns dos prompts durante o processo de entrada em podem ser exibida mais de uma vez, mesmo se parecem ser o mesmo. Quando isso acontece, continue seguindo as instruções de entrar.

1. Depois que você tiver conectado com êxito para sua conta do Azure, a caixa de diálogo **Gerenciar inscrições** exibirá uma lista de assinaturas que estão associadas a suas credenciais. Se houver várias assinaturas listadas e você quer trabalhar com apenas um subconjunto específico deles, opcionalmente, você pode desmarcar aquelas que você deseja usar. Quando você tiver selecionado suas assinaturas, clique em **Fechar**.

   ![][08]

1. Quando for exibida a caixa de diálogo **implantar ao contêiner do Azure Web App** , ele exibirá qualquer contêineres de aplicativo da Web que você criou anteriormente; Se você não tiver criado qualquer contêineres, a lista estará vazia.   

   ![][09]

1. Se você não tiver criado um contêiner de aplicativo Web do Azure antes, ou se você gostaria de publicar seu aplicativo para um novo recipiente, use as etapas a seguir. Caso contrário, selecione um contêiner de aplicativo da Web existente e vá para a etapa 6 abaixo.

  1. Clique em**+**

        ![][10]

  1. Será exibida a caixa de diálogo **Novo contêiner de aplicativo da Web** , que será usado para as próximas etapas.

        ![][11]

  1. Digite um **rótulo DNS** para seu contêiner de aplicativo Web; Isto irá formulário o rótulo DNS folha da URL do host para o aplicativo web no Azure. Observação: O nome deve estar disponível e estar em conformidade com requisitos de nomenclatura do Azure Web App.

  1. No menu suspenso **Contêiner da Web** , selecione o software apropriado para o seu aplicativo.

        Atualmente, você pode escolher entre Tomcat 8, Tomcat 7 ou Jetty 9. Uma distribuição recente do software selecionado será fornecida pelo Azure e ele será executado em uma distribuição recente do JDK 8 criada pela Oracle e fornecida pelo Azure.

  1. No menu suspenso de **assinatura** , selecione a assinatura que você deseja usar para essa implantação.

  1. No menu suspenso **Grupo de recursos** , selecione o grupo de recursos com o qual você deseja associar seu aplicativo Web.

        Observação: O Azure grupos de recursos permitem que você agrupar recursos relacionados para que, por exemplo, pode ser excluídos juntos.

        Você pode selecionar um grupo de recursos existente (se houver) e pular para a etapa g abaixo ou use o seguinte estas etapas para criar um novo grupo de recursos:

      * Clique em **novo …**

      * Caixa de diálogo **Novo grupo de recursos** será exibida:

            ![][12]

      * Na caixa de texto **nome** , especifique um nome para seu novo grupo de recursos.

      * No menu de lista suspensa de **região** , selecione os apropriado Azure Centro de dados local para seu grupo de recursos.

      * Clique em **Okey**.

  1. O menu suspenso de **Plano de serviço de aplicativo** lista os planos de serviço de aplicativo que são associados com o grupo de recursos que você selecionou.

        Observação: Um plano de serviço de aplicativo especifica informações como o local de seu aplicativo Web, a camada de preços e o tamanho da instância de computação. Um único plano de serviço de aplicativo pode ser usado para vários aplicativos Web, o motivo pelo qual ele é mantido separadamente de uma implantação específica do Web App.

        Você pode selecionar um existente aplicativo serviço planejar (se houver) e pular para a etapa h abaixo ou use o seguinte estas etapas para criar um novo plano de serviço de aplicativo:

      * Clique em **novo …**

      * A caixa de diálogo **Novo plano de serviço de aplicativo** será exibida:

            ![][13]

      * Na caixa de texto **nome** , especifique um nome para seu novo plano de serviço de aplicativo.

      * No menu de lista suspensa **local** , selecione os apropriado Azure Centro de dados local para o plano.

      * No menu suspenso **Camada de preços** , selecione os preços apropriado para o plano. Para fins de teste, você pode escolher **livre**.

      * No **Tamanho da instância** suspensa menu, selecione a instância apropriada de tamanho para o plano. Para fins de teste, você pode escolher **pequenas**.

  1. Quando tiver concluído todas as etapas acima, a caixa de diálogo novo contêiner de aplicativo Web deve ser semelhante a ilustração a seguir:

        ![][14]

  1. Clique em **Okey** para concluir a criação de seu novo contêiner Web App.

        Aguarde alguns segundos para a lista dos contêineres Web App para ser atualizado e recipiente web recém-criado aplicativo agora deve ser selecionado na lista.

1. Agora você está pronto para concluir a implantação inicial do seu aplicativo Web para o Azure; Clique em **Okey** para implantar seu aplicativo Java ao recipiente Web App selecionado.

    ![][15]

    Observação: Por padrão, o aplicativo será implantado como uma subpasta do servidor do aplicativo. Se você deseja que ele para ser implantado como o aplicativo raiz, marque a caixa de seleção **implantar a raiz** antes de clicar em **Okey**.

1. Em seguida, você verá a exibição de **Log de atividade do Azure** , que indica o status de implantação de seu aplicativo Web.

    ![][16]

    Processo de implantação do seu aplicativo Web para o Azure deve levar apenas alguns segundos para ser concluída. Quando pronto seu aplicativo, você verá um link chamado **publicado** na coluna **Status** . Quando você clica no link, levará você à home page do seu aplicativo Web implantado ou você pode usar as etapas na seção a seguir para procurar seu aplicativo web.

## <a name="browsing-to-your-web-app-on-azure"></a>Navegando até o aplicativo Web do Azure

Para brows para o seu aplicativo Web no Azure, você pode usar o modo de exibição do **Explorer do Azure** .

Se o modo de exibição do **Azure Explorer** não ainda estiver aberto, você pode abri-lo clicando em seguida, menu **Exibir** em IntelliJ, e em seguida, clique em **Ferramenta Windows**e, em seguida, clique em **Gerenciador de serviço**. Se você não tiver conectado anteriormente, ele avisará para fazê-lo.

Quando o modo de exibição do **Azure Explorer** for exibido, use siga estas etapas para interromper o seu aplicativo Web: 

1. Expanda o nó do **Azure** .

1. Expanda o nó de **Aplicativos Web** . 

1. Clique com botão direito do Web App desejado.

1. Quando o menu de contexto aparece, clique em **Abrir no navegador**.

    ![][17]

## <a name="updating-your-web-app"></a>Atualizando seu aplicativo Web

Atualizar uma existente executando o Azure Web App é um processo rápido e fácil e você tem duas opções para a atualização:

* Você pode atualizar a implantação do aplicativo Web do Java existente.
* Você pode publicar um aplicativo Java adicional ao mesmo recipiente de aplicativo Web.

Nos dois casos, o processo é idêntico e leva apenas alguns segundos:

1. No Explorador de projeto IntelliJ, clique no aplicativo de Java que você deseja atualizar ou adicionar a um contêiner de aplicativo da Web existente.

1. Quando aparece o menu de contexto, selecione **Azure** e **Publicar como Azure Web App....**

1. Desde que você já fez anteriormente, você verá uma lista dos seus contêineres de aplicativo da Web existentes. Selecione a que deseja publicar ou republicar em seu aplicativo Java e clique em **Okey**.

Alguns segundos mais tarde, o modo de exibição de **Log de atividade do Azure** mostrará sua implantação atualizada como **publicado** e você será capaz de verificar seu aplicativo atualizado em um navegador da web.

## <a name="starting-or-stopping-an-existing-web-app"></a>Iniciar ou interromper um aplicativo Web existente

Para iniciar ou interromper um contêiner existente do Azure Web App, (incluindo todos os aplicativos Java implantados nele), você pode usar o modo de exibição do **Explorer do Azure** .

Se o modo de exibição do **Azure Explorer** não ainda estiver aberto, você pode abri-lo clicando em seguida, menu **Exibir** em IntelliJ, e em seguida, clique em **Ferramenta Windows**e, em seguida, clique em **Gerenciador de serviço**. Se você não tiver conectado anteriormente, ele avisará para fazê-lo.

Quando o modo de exibição do **Azure Explorer** for exibido, use siga estas etapas para iniciar ou interromper o aplicativo Web: 

1. Expanda o nó do **Azure** .

1. Expanda o nó de **Aplicativos Web** . 

1. Clique com botão direito do Web App desejado.

1. Quando o menu de contexto aparece, clique em **Iniciar** ou **Parar**. Observe que as opções de menu são reconhecimento de contexto, para que você só pode parar um aplicativo web em execução ou iniciar um aplicativo web que não está sendo executado.

    ![][18]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os kits de ferramentas do Azure para IDEs Java, consulte os links a seguir:

- [Kit de ferramentas de Azure para Eclipse]
  - [Instalando o Kit de ferramentas do Azure para Eclipse]
  - [Criar um aplicativo de Web Olá mundo para Azure no Eclipse]
  - [Novidades no Azure Kit de ferramentas para Eclipse]
- [Kit de ferramentas Azure para IntelliJ]
  - [Instalando o Kit de ferramentas do Azure para IntelliJ]
  - *Criar um aplicativo de Web Olá mundo para Azure no IntelliJ (neste artigo)*
  - [Novidades no Azure Kit de ferramentas para IntelliJ]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

Para obter informações adicionais sobre a criação de aplicativos Web do Azure, consulte [Visão geral de aplicativos Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de ferramentas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de ferramentas Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalando o Kit de ferramentas do Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalando o Kit de ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novidades no Azure Kit de ferramentas para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novidades no Azure Kit de ferramentas para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/
[Visão geral de aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png
