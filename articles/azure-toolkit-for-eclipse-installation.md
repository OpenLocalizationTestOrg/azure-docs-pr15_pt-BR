<properties
    pageTitle="Instalando o Kit de ferramentas do Azure para Eclipse | Microsoft Azure"
    description="Saiba como instalar o Kit de ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalando o Kit de ferramentas do Azure para Eclipse

O Kit de ferramentas do Azure para Eclipse fornece modelos e funcionalidades que permitem que você criar, desenvolver, testar e implantar aplicativos do Azure usando o ambiente de desenvolvimento do Eclipse. O Kit de ferramentas do Azure para Eclipse é um projeto de código-fonte aberto, cujo código fonte está disponível sob a licença do MIT do site do projeto no GitHub na seguinte URL:

<https://GitHub.com/Microsoft/Azure-Tools-for-Java>

As etapas a seguir mostram como instalar o Kit de ferramentas do Azure para Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Para instalar o Kit de ferramentas do Azure para Eclipse

1. Inicie o Eclipse.

1. Quando Eclipse é aberta, clique no menu **Ajuda** e, em seguida, clique em **Instalar novo Software**, conforme mostrado na ilustração a seguir.

    ![Instalando o Kit de ferramentas do Azure para Eclipse][01]

1. Na caixa de diálogo **Software disponível** , dentro da caixa de texto de **trabalhar com** , digite **http://dl.microsoft.com/eclipse** seguido da tecla **Enter** .

1. No painel de **nome** , marque o **Kit de ferramentas do Azure para Eclipse**e desmarque **contato todos atualizar sites durante a instalação para localizar software necessário**. Sua tela deverá aparecer similar ao seguinte:

    ![Instalando o Kit de ferramentas do Azure para Eclipse][02]

1. Se você expandir o **Kit de ferramentas do Azure para Eclipse**, você verá os seguintes itens:

    * **Plug-in do aplicativo ideias para Java**: este componente permite que você use os serviços do Azure análise e registro em log de telemetria para seus aplicativos e instâncias do servidor.
    * **Filtro de serviços de controle de acesso do Azure**: este componente oferece suporte para autenticar usuários de aplicativo com o Azure ACS, ativar cenários de logon único e externalização lógica da identidade do aplicativo.
    * **Plug-in do Azure comuns**: este componente fornece a funcionalidade comum necessária por outros componentes do Kit de ferramentas.
    * **Azure Explorer para Eclipse**: este componente fornece a funcionalidade comum necessária por outros componentes do Kit de ferramentas.
    * **Plug-in do Azure para Eclipse com Java**: este componente fornece suporte para desenvolver projetos que ajudam a construir, testar e implantar aplicativos Java para a nuvem do Microsoft Azure no Eclipse e através da linha de comando.
    * **Plug-in do Azure Web Apps com Java**: este componente fornece suporte para implantar aplicativos da web de Java em contêineres do Microsoft Azure Web App.
    * **Microsoft JDBC Driver 4.2 para SQL Server**: este componente fornece API JDBC para SQL Server e o banco de dados do Microsoft Azure SQL para Java Platform Enterprise Edition 8.
    * **Pacote para as bibliotecas de cliente do Apache Qpid de JMS**: este componente fornece o componente de cliente JMS do projeto Apache Qpid para habilitar o seu aplicativo para usar AMQP de mensagens no Microsoft Azure.
    * **Pacote para bibliotecas do Microsoft Azure para Java**: este componente fornece APIs para acessar os serviços do Microsoft Azure, como armazenamento, barramento de serviço, o tempo de execução do serviço, etc.

1. Clique em **Avançar**. (Se você tiver atrasos incomuns ao instalar o Kit de ferramentas, verifique se o **contato todos atualizar sites durante a instalação para localizar software necessário** está desmarcada.)

1. Na caixa de diálogo **Detalhes de instalação** , clique em **Avançar**.

    ![Examine os detalhes de instalação][03]

1. Na caixa de diálogo **Licenças Revise** , revise os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **aceito os termos dos contratos de licença** e clique em **Concluir**. (As etapas restantes presumem que você aceite os termos dos contratos de licença. Se você não aceitar os termos dos contratos de licença, saia o processo de instalação.)

    ![Licenças de revisão][04]

    Eclipse será Baixe e instale os pacotes necessários.

    ![Progresso da instalação][05]

1. Se solicitado a reiniciar o Eclipse para concluir a instalação, clique em **Sim**.

    ![Reinicie o Prompt][06]

## <a name="see-also"></a>Consulte também

Para obter mais informações sobre os kits de ferramentas do Azure para IDEs Java, consulte os links a seguir:

- [Kit de ferramentas de Azure para Eclipse]
  - *Instalando o Kit de ferramentas do Azure para Eclipse (neste artigo)*
  - [Criar um aplicativo de Web Olá mundo para Azure no Eclipse]
  - [Novidades no Azure Kit de ferramentas para Eclipse]
- [Kit de ferramentas Azure para IntelliJ]
  - [Instalando o Kit de ferramentas do Azure para IntelliJ]
  - [Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]
  - [Novidades no Azure Kit de ferramentas para IntelliJ]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Kit de ferramentas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ferramentas Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalando o Kit de ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Azure Kit de ferramentas para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Azure Kit de ferramentas para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

