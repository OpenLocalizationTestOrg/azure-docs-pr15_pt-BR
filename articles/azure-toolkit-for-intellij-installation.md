<properties
    pageTitle="Instalando o Kit de ferramentas do Azure para IntelliJ | Microsoft Azure"
    description="Saiba como instalar o Kit de ferramentas do Azure para a IDEIA IntelliJ."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="installing-the-azure-toolkit-for-intellij"></a>Instalando o Kit de ferramentas do Azure para IntelliJ

O Kit de ferramentas do Azure para IntelliJ fornece modelos e funcionalidades que permitem que você criar, desenvolver, testar e implantar aplicativos do Azure usando o ambiente de desenvolvimento IntelliJ IDEIA. O Kit de ferramentas do Azure para IntelliJ é um projeto de código-fonte aberto, cujo código fonte está disponível sob a licença do MIT do site do projeto no GitHub na seguinte URL:

<https://GitHub.com/Microsoft/Azure-Tools-for-Java>

Há dois métodos de instalar o Kit de ferramentas do Azure para IntelliJ, na caixa de diálogo de configurações e do menu Configurar na tela Iniciar; ambos os métodos de instalação serão ser demonstrou nas etapas a seguir.

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Para instalar o Kit de ferramentas do Azure para IntelliJ a partir da caixa de diálogo de configurações

1. Inicie IntelliJ IDEIA.

1. Quando a IDEIA IntelliJ for aberto, clique em **arquivo**, clique em **configurações**.

    ![Abrir a caixa de diálogo de configurações de IDEIA IntelliJ][01a]

1. Na caixa de diálogo Configurações, clique em **Plug-ins**e, em seguida, clique em **Procurar repositórios**.

    ![Caixa de diálogo de configurações de IDEIA IntelliJ][02a]

1. Na caixa de diálogo **Procurar repositórios** , digite "Azure" na caixa de pesquisa. Realce o **Kit de ferramentas do Azure para IntelliJ**e clique em **instalar**.

    ![Procure o Kit de ferramentas do Azure para IntelliJ][03]

    IntelliJ IDEIA exibirá o andamento da instalação em uma caixa de diálogo.

    ![Progresso da instalação][04]

1. Quando a instalação estiver concluída, clique em **Reiniciar IntelliJ IDEIA**.

    ![Reinicie o IntelliJ IDEIA][05]

1. Clique em **Okey** para fechar a caixa de diálogo de configurações.

    ![Feche a caixa de diálogo de configurações de IDEIA IntelliJ][06]

1. Quando solicitado a reiniciar IntelliJ IDEIA ou adiar, clique em **Reiniciar**.

    ![Reinicie o IntelliJ IDEIA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Para instalar o Kit de ferramentas do Azure para IntelliJ desde a tela inicial

1. Inicie IntelliJ IDEIA.

1. Quando a tela inicial de IDEIA IntelliJ for exibida, clique em **Configurar**, clique em **Plug-ins**.

    ![Instalar o plug-ins do IntelliJ IDEIA][01b]

1. Na caixa de diálogo **Plug-ins** , clique em **Procurar repositórios**.

    ![Procurar repositórios de plug-in de IDEIA IntelliJ][02b]

1. Na caixa de diálogo **Procurar repositórios** , digite "Azure" na caixa de pesquisa. Realce o **Kit de ferramentas do Azure para IntelliJ**e clique em **instalar**.

    ![Procure o Kit de ferramentas do Azure para IntelliJ][03]

    IntelliJ IDEIA exibirá o andamento da instalação em uma caixa de diálogo.

    ![Progresso da instalação][04]

1. Quando a instalação estiver concluída, clique em **Reiniciar IntelliJ IDEIA**.

    ![Reinicie o IntelliJ IDEIA][05]

1. Quando solicitado a reiniciar IntelliJ IDEIA ou adiar, clique em **Reiniciar**.

    ![Reinicie o IntelliJ IDEIA][07]

## <a name="see-also"></a>Consulte também

Para obter mais informações sobre os kits de ferramentas do Azure para IDEs Java, consulte os links a seguir:

- [Kit de ferramentas de Azure para Eclipse]
  - [Instalando o Kit de ferramentas do Azure para Eclipse]
  - [Criar um aplicativo de Web Olá mundo para Azure no Eclipse]
  - [Novidades no Azure Kit de ferramentas para Eclipse]
- [Kit de ferramentas Azure para IntelliJ]
  - *Instalar o Kit de ferramentas Azure for IntelliJ (neste artigo)*
  - [Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]
  - [Novidades no Azure Kit de ferramentas para IntelliJ]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Kit de ferramentas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ferramentas Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalando o Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Azure Kit de ferramentas para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Azure Kit de ferramentas para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png
