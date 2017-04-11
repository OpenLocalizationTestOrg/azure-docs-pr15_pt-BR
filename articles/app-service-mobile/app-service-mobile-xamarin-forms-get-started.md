<properties
    pageTitle="Introdução aos aplicativos Mobile usando Xamarin.Forms"
    description="Siga este tutorial para começar a usar os aplicativos do Azure Mobile para desenvolvimento de Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Criar um aplicativo de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço baseado em nuvem back-end a um aplicativo móvel Xamarin.Forms usando um back-end do aplicativo do Azure Mobile. Você criará um novo back-end do aplicativo móvel e uma simples _lista de Todo_ aplicativo Xamarin.Forms que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de aplicativos móveis para Xamarin.Forms.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa do seguinte:

* Uma conta do Azure active. Se você não tiver uma conta, você pode inscrever-se para uma avaliação do Azure e obter até 10 gratuitos aplicativos móveis que você pode continuar usando mesmo após o período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções. 

* Um Mac versão Xcode 7.0 ou posterior e Xamarin Studio comunidade instalado. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [instalação, instalar e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](https://tryappservice.azure.com/?appServiceName=mobile), onde você pode criar imediatamente um aplicativo móvel de início curta duração no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end do aplicativo do Azure Mobile

Siga estas etapas para criar um novo back-end do aplicativo Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Agora você tem provisionado um back-end do aplicativo do Azure Mobile que pode ser usado por seus aplicativos de cliente móvel. Em seguida, você irá baixar um projeto de servidor para um simples "lista de tarefas pendentes" back-end e publicá-lo do Azure.

## <a name="configure-the-server-project"></a>Configurar o project server

Siga as etapas abaixo para configurar o projeto de servidor para usar o Node ou .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Baixar e executar a solução de Xamarin.Forms

Aqui, você tem algumas opções. Você pode baixar a solução para um Mac e abri-lo no Xamarin Studio, ou você pode baixar a solução para um computador Windows e abri-lo no Visual Studio usando um Mac em rede para criar o aplicativo iOS. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) se precisar de instruções mais detalhadas sobre os cenários de instalação Xamarin.

Vamos prosseguir:

 1. No seu Mac ou no seu computador com Windows, abra o [Portal do Azure] na janela do navegador.
 2. Na lâmina configurações para o aplicativo móvel, clique em **Introdução** (em Mobile) > **Xamarin.Forms**. Em etapa 3, clique em **criar um novo aplicativo** se ele ainda não estiver selecionado.  Em seguida, clique no botão **Download** .

    Isso faz o download de um projeto que contém um aplicativo cliente que está conectado ao seu aplicativo móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde você salvá-lo.

 3. Extrair o projeto que você baixou e abra-a no Xamarin Studio ou Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto do iOS

Esta seção é para execução do projeto de iOS Xamarin para dispositivos iOS. Se não estiver trabalhando com dispositivos iOS, você pode pular esta seção.

####<a name="in-xamarin-studio"></a>No Xamarin Studio

1. Clique com botão direito do projeto do iOS e clique em **Definir como projeto de inicialização**.
2. No menu **Executar** , clique em **Iniciar depuração** para criar o projeto e iniciar o aplicativo no emulador iPhone.

####<a name="in-visual-studio"></a>No Visual Studio
1. Clique com botão direito do projeto do iOS e, em seguida, clique em **Definir como projeto de inicialização**.
2. No menu **Build** , clique em **Gerenciador de configuração**.
3. Na caixa de diálogo **Gerenciador de configuração** , marque as caixas de seleção **Construir** e **implantar** do projeto iOS.
4. Pressione a tecla **F5** para construir o projeto e iniciar o aplicativo no emulador iPhone.

    >[AZURE.NOTE] Se você tiver problemas para criar, executar NuGet Gerenciador de pacote e a atualização da versão mais recente da Xamarin suportam pacotes. Às vezes, os projetos de início rápido podem ficar em sendo atualizadas para o mais recente.    

No aplicativo, digite o texto significativo, como _Saiba Xamarin_ e clique o **+** botão.

![][10]

Isso envia uma solicitação de POSTAGEM para o novo aplicativo móvel back-end hospedado no Azure. Dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo aplicativo móvel back-end e os dados são exibidos na lista.

>[AZURE.NOTE]
> Você encontrará o código que acessa seu back-end do aplicativo móvel no arquivo TodoItemManager.cs c# do projeto da biblioteca de classes portátil da sua solução.

##<a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto Android

Esta seção é para execução do projeto de droid de Xamarin para Android. Você pode pular esta seção se você não estiver trabalhando com dispositivos Android.

####<a name="in-xamarin-studio"></a>No Xamarin Studio

1. Clique com botão direito do projeto Android e clique em **Definir como projeto de inicialização**.
2. No menu **Executar** , clique em **Iniciar depuração** para criar o projeto e iniciar o aplicativo em um emulador Android.

####<a name="in-visual-studio"></a>No Visual Studio
1. Clique com botão direito do projeto Android (Droid) e, em seguida, clique em **Definir como projeto de inicialização**.
4. No menu **Build** , clique em **Gerenciador de configuração**.
5. Na caixa de diálogo **Gerenciador de configuração** , marque as caixas de seleção **Construir** e **implantar** do projeto Android.
6. Pressione a tecla **F5** para construir o projeto e iniciar o aplicativo em um emulador Android.

    >[AZURE.NOTE] Se você tiver problemas para criar, executar NuGet Gerenciador de pacote e a atualização da versão mais recente da Xamarin suportam pacotes. Às vezes, os projetos de início rápido podem ficar em sendo atualizadas para o mais recente.    


No aplicativo, digite o texto significativo, como _Saiba Xamarin_ e clique o **+** botão.

![][11]

Isso envia uma solicitação de POSTAGEM para o novo aplicativo móvel back-end hospedado no Azure. Dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo aplicativo móvel back-end e os dados são exibidos na lista.

> [AZURE.NOTE]
> Você encontrará o código que acessa seu back-end do aplicativo móvel no arquivo TodoItemManager.cs c# do projeto da biblioteca de classes portátil da sua solução.


##<a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto do Windows


Esta seção é para execução do projeto Xamarin WinApp para dispositivos Windows. Você pode pular esta seção se você não estiver trabalhando com dispositivos Windows.


####<a name="in-visual-studio"></a>No Visual Studio
1. Clique com botão direito nenhum dos projetos Windows e, em seguida, clique em **Definir como projeto de inicialização**.
4. No menu **Build** , clique em **Gerenciador de configuração**.
5. Na caixa de diálogo **Gerenciador de configuração** , marque as caixas de seleção **Construir** e **implantar** de projeto do Windows que você escolheu.
6. Pressione a tecla **F5** para construir o projeto e iniciar o aplicativo em um emulador do Windows.

    >[AZURE.NOTE] Se você tiver problemas para criar, executar NuGet Gerenciador de pacote e a atualização da versão mais recente da Xamarin suportam pacotes. Às vezes, os projetos de início rápido podem ficar em sendo atualizadas para o mais recente.    


No aplicativo, digite o texto significativo, como _Saiba Xamarin_ e clique o **+** botão.

Isso envia uma solicitação de POSTAGEM para o novo aplicativo móvel back-end hospedado no Azure. Dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo aplicativo móvel back-end e os dados são exibidos na lista.

![][12]

> [AZURE.NOTE]
> Você encontrará o código que acessa seu back-end do aplicativo móvel no arquivo TodoItemManager.cs c# do projeto da biblioteca de classes portátil da sua solução.

##<a name="next-steps"></a>Próximas etapas

* [Adicione autenticação ao seu aplicativo](app-service-mobile-xamarin-forms-get-started-users.md)  
Saiba como autenticar os usuários do aplicativo com um provedor de identidade.

* [Adicionar notificações por push para o seu aplicativo](app-service-mobile-xamarin-forms-get-started-push.md)  
Saiba como adicionar push notificações de suporte para o seu aplicativo e configurar seu back-end do aplicativo móvel para usar Hubs de notificação do Azure para enviar notificações por push.

* [Habilitar a sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline seu aplicativo usando um back-end do aplicativo Mobile. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede.

* [Como usar o cliente gerenciado para aplicativos do Azure móvel](app-service-mobile-dotnet-how-to-use-client-library.md)  
Aprenda a trabalhar com o cliente gerenciado SDK em seu aplicativo de Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/

