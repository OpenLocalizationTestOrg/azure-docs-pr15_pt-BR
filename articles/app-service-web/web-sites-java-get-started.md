<properties
    pageTitle="Criar um aplicativo da web de Java no serviço de aplicativo do Azure | Microsoft Azure"
    description="Este tutorial mostra como implantar um aplicativo da web de Java ao serviço de aplicativo do Azure."
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
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Criar um aplicativo da web de Java no serviço de aplicativo do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar um Java [web app no serviço de aplicativo do Azure] usando o [Portal do Azure]. Portal do Azure é uma interface de web que você pode usar para gerenciar os recursos do Azure.

> [AZURE.NOTE] Para concluir este tutorial, você precisará de uma conta do Microsoft Azure. Se você não tiver uma conta, você pode [ativar seus benefícios de assinante do Visual Studio] ou [inscrever-se para uma avaliação gratuita].
>
> Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo]. Lá, você pode criar imediatamente um aplicativo da web de curta duração starter no serviço de aplicativo; Nenhum cartão de crédito for necessária e nenhum compromissos.

## <a name="java-application-options"></a>Opções de aplicativo Java

Há várias maneiras que você pode configurar um aplicativo de Java em um serviço de aplicativo web app. 

1. Criar um aplicativo e, em seguida, definir **configurações de aplicativo**.

    Serviço de aplicativo oferece várias versões de Tomcat e Jetty, com a configuração padrão. Se o aplicativo que você hospedará funcionará com uma das versões internas, esse método de configuração de um contêiner da web é mais fácil e é ideal quando tudo o que você deseja fazer é carregar um arquivo de guerra em um contêiner da web. Para este método, você cria um aplicativo no Portal do Azure e vá para a lâmina de **configurações do aplicativo** para o aplicativo escolher a versão do Java juntamente com o contêiner de web Java desejado. Quando você usa esse método Java e recipiente web são executados de arquivos de programa. Outros métodos de colocar o recipiente da web e potencialmente JVM em seu espaço de disco. Quando você usa esse modelo, você não tem acesso para editar arquivos nesta parte do sistema de arquivos. Isso significa que você não pode fazer coisas como configurar o arquivo *XML* ou coloque arquivos da biblioteca na pasta */lib* . Para obter mais informações, consulte o [criar e configurar um aplicativo da web de Java](#appsettings) seção mais adiante neste tutorial.
    
2. Use um modelo do Azure Marketplace.

    Do Azure Marketplace inclui modelos que automaticamente criar e configurar aplicativos da web de Java com contêineres de web Tomcat ou Jetty. Os contêineres de web que criam os modelos são configuráveis. Para obter mais informações, consulte a seção [usar um modelo de Java do Azure Marketplace](#marketplace) deste tutorial.
  
3. Criar um aplicativo e, em seguida, copie manualmente e editar arquivos de configuração 

    Talvez você queira hospedar um aplicativo personalizado do Java que não implantar em qualquer um dos contêineres web fornecidos pelo serviço de aplicativo. Por exemplo:
    
    * Seu aplicativo Java requer uma versão do Tomcat ou Jetty que não é diretamente suportado pelo serviço de aplicativo ou fornecido na Galeria.
    * Seu aplicativo Java leva solicitações HTTP e não implantar como uma guerra em um contêiner da web preexistente.
    * Você deseja configurar o recipiente da web do zero por conta própria. 
    * Você deseja usar uma versão do Java que não tenha suporte no aplicativo de serviço e deseja carregá-lo.

    Para casos como esses, você pode criar um aplicativo usando o Portal do Azure e fornecer os arquivos de tempo de execução apropriado manualmente. Nesse caso, os arquivos serão contados contra suas cotas de espaço de armazenamento para seu plano de serviço de aplicativo. Para obter mais informações, consulte [carregar um aplicativo de web Java personalizado para o Azure].

## <a name="portal"></a>Criar e configurar um aplicativo da web de Java

Esta seção mostra como criar um aplicativo web e configurá-lo para Java usando a lâmina de **configurações do aplicativo** do portal.

1. Entre [Portal do Azure].

2. Clique em **Novo > Web + Mobile > Web App**.

    ![Novo aplicativo Web][newwebapp]

4. Insira um nome para o aplicativo da web na caixa **Web app** .

    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo web será {name}. azurewebsites.net. Se o nome inserido não exclusivo, um ponto de exclamação vermelho aparece na caixa de texto.

5. Selecione um **Grupo de recursos** ou crie um novo.

    Para obter mais informações sobre os grupos de recursos, consulte [usando o Portal do Azure para gerenciar seus recursos Azure].

6. Selecione um **Local da plano de serviço de aplicativo** ou crie um novo.

    Para obter mais informações sobre planos de serviço de aplicativo, consulte [Visão geral de planos de serviço de aplicativo do Azure].

7. Clique em **criar**.

    ![Crie aplicativo Web][newwebapp2]
 
8. Quando o aplicativo web tiver sido criado, clique em **Web Apps > {seu aplicativo web}**.
 
    ![Selecione Web App][selectwebapp]

9. Na lâmina **Web app** , clique em **configurações**.

10. Clique em **configurações do aplicativo**.

11. Escolha a **versão Java**de desejada. 

12. Escolha a **versão secundária Java**de desejada. Se você selecionar **mais novo**, o seu aplicativo usará a versão secundária mais recente disponível no aplicativo de serviço para essa versão principal Java. O **mais novo** item é exclusivo para aplicativos de Java criados a partir de **configurações do aplicativo**. Se você criar seu aplicativo Java da galeria, você precisa gerenciar seu próprio contêiner e alterações JVM. 

12. Escolha o desejado **contêiner da Web**. Se você selecionar um nome de contêiner que começa com o **mais novo**, seu aplicativo será mantido na versão mais recente dessa versão principal de contêiner de web que está disponível no aplicativo de serviço. 

    ![Versões de contêiner de Web][versions]

13. Clique em **Salvar**.

    Em alguns momentos, o seu aplicativo web se tornará baseado em Java e configurado para usar o recipiente da web que você selecionou.

14. Clique em **aplicativos Web > {seu novo aplicativo web}**.

15. Clique na **URL** para navegar para o novo site.

    A página da web confirma que você tenha criado um aplicativo web baseado em Java.

## <a name="marketplace"></a>Usar um modelo de Java do Azure Marketplace

Esta seção mostra como usar o Azure Marketplace para criar um aplicativo da web de Java. O mesmo fluxo geral também pode ser usado para criar um celular baseado em Java ou o aplicativo de API. 

1. Entre [Portal do Azure]

2. Clique em **Novo > Marketplace**.

    ![Novo Marketplace][newmarketplace]

3. Clique em **Web + Mobile**.

    Talvez seja necessário rolar para a esquerda para ver a lâmina **Marketplace** onde você pode selecionar **Web + Mobile**.

4. Na caixa de texto Pesquisar, digite o nome de um servidor de aplicativo Java, como o **Apache Tomcat** ou **Jetty**e pressione Enter.

5. Nos resultados da pesquisa, clique no servidor de aplicativo Java.

    ![Jetty móvel da Web][webmobilejetty]

6. Na primeira lâmina **Apache Tomcat** ou **Jetty** , clique em **criar**.

    ![Jetty lâmina Portal][jettyblade]

7. Na próxima lâmina **Apache Tomcat** ou **Jetty** , insira um nome para o aplicativo da web na caixa **Web app** .

    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo web será {name}. azurewebsites.net. Se o nome inserido não exclusivo, um ponto de exclamação vermelho aparece na caixa de texto.

8. Selecione um **Grupo de recursos** ou crie um novo.

    Para obter mais informações sobre os grupos de recursos, consulte [usando o Portal do Azure para gerenciar seus recursos Azure].

9. Selecione um **Local da plano de serviço de aplicativo** ou crie um novo.

    Para obter mais informações sobre planos de serviço de aplicativo, consulte [Visão geral de planos de serviço de aplicativo do Azure].

10. Clique em **criar**.

    ![Criar Portal jetty][jettyportalcreate2]

    Em um pouco tempo, geralmente menores do que um minuto, Azure termina de criar o novo aplicativo web.

11. Clique em **aplicativos Web > {seu novo aplicativo web}**.

12. Clique na **URL** para navegar para o novo site.

    ![URL jetty][jettyurl]

    Tomcat vem com um conjunto padrão de páginas; Se você escolheu Tomcat, você verá uma página semelhante ao exemplo a seguir.

    ![Web app usando Apache Tomcat][tomcat]

    Se você escolheu Jetty, você verá uma página semelhante ao exemplo a seguir. Jetty não tem um conjunto de página padrão, portanto, JSP mesmo que é usado para um site de Java vazio é reutilizado aqui.

    ![Web app usando Jetty][jetty]

Agora que você criou o aplicativo web com um contêiner de aplicativo, consulte a seção [próximas etapas](#next-steps) para obter informações sobre como carregar seu aplicativo para o aplicativo web.

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você tem um servidor de aplicativo Java executado em seu aplicativo web do serviço de aplicativo do Azure. Para implantar o seu próprio código para o aplicativo da web, consulte [Adicionar um aplicativo ou página da Web para o aplicativo web do Java].

Para obter mais informações sobre como desenvolver aplicativos Java no Azure, consulte o [Java Developer Center].

<!-- URL List -->

[Adicionar um aplicativo ou página da Web ao seu aplicativo web do Java]: ./web-sites-java-add-app.md
[Visão geral de planos de serviço de aplicativo do Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal do Azure]: https://portal.azure.com/
[ativar os benefícios de assinante do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Experimente o aplicativo de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[Web app no serviço de aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Central de desenvolvedores de Java]: /develop/java/
[Usando o Portal do Azure para gerenciar os recursos do Azure]: ../azure-portal/resource-group-portal.md
[Carregar um aplicativo web personalizado do Java no Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
