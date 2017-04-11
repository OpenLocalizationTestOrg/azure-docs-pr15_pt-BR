<properties
    pageTitle="Introdução ao Azure aplicativos móveis para aplicativos de Xamarin.Android"
    description="Siga este tutorial para começar a usar os aplicativos do Azure Mobile para desenvolvimento de Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Criar um aplicativo de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço baseado em nuvem back-end a um aplicativo de Xamarin.Android. Para obter mais informações, consulte [o que são aplicativos Mobile](app-service-mobile-value-prop.md).

Uma captura de tela do aplicativo concluída está abaixo:

![][0]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de aplicativos móveis para os aplicativos de Xamarin.Android.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará os seguintes pré-requisitos:

* Uma conta do Azure active. Se você não tiver uma conta, inscreva-se para uma avaliação do Azure e obter até 10 aplicativos móveis gratuito. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.

>[AZURE.NOTE]Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](https://tryappservice.azure.com/?appServiceName=mobile).  Imediatamente, você pode criar um aplicativo móvel de início curta duração no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end do aplicativo do Azure Mobile

Siga estas etapas para criar um back-end do aplicativo Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Agora você tem provisionado um back-end do aplicativo do Azure Mobile que pode ser usado por seus aplicativos de cliente móvel. Em seguida, baixe um projeto de servidor para um simples "lista de tarefas pendentes" back-end e publicá-lo do Azure.

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Baixar e executar o aplicativo de Xamarin.Android

1. Em **baixar e executar o projeto Xamarin.Android**, clique no botão **Baixar** .

    Salve o arquivo de projeto compactado em seu computador local e anote onde você salvá-lo.

2. Pressione a tecla **F5** para construir o projeto e iniciar o aplicativo.

3. No aplicativo, digite o texto significativo, como _concluída o tutorial_ e, em seguida, clique no botão **Adicionar** .

    ![][10]

    Dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo aplicativo móvel back-end e os dados aparecem na lista.

    > [AZURE.NOTE] Você pode examinar o código que acessa seu back-end do aplicativo móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.cs c#.

##<a name="next-steps"></a>Próximas etapas

* [Adicionar uma sincronização Offline para o seu aplicativo](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Adicione autenticação ao seu aplicativo](app-service-mobile-xamarin-android-get-started-users.md)
* [Adicionar notificações por push para o seu aplicativo de Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Como usar o cliente gerenciado para aplicativos do Azure móvel](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
