<properties
    pageTitle="Introdução aos aplicativos do Azure aplicativo serviço Mobile para aplicativos de Xamarin.iOS | Microsoft Azure"
    description="Siga este tutorial para começar com o uso de aplicativos móveis para desenvolvimento de Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Criar um aplicativo de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço baseado em nuvem back-end para um aplicativo móvel Xamarin.iOS usando um back-end do aplicativo móvel Azure.  Crie um novo back-end do aplicativo móvel e uma simples _lista de Todo_ aplicativo Xamarin.iOS que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Xamarin.iOS sobre como usar o recurso de aplicativos Mobile no serviço de aplicativo do Azure.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará os seguintes pré-requisitos:

* Uma conta do Azure active. Se você não tiver uma conta, inscreva-se em uma versão de avaliação do Azure e obter até 10 livre em aplicativos móvel que você pode continuar usando mesmo após o período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.

* Um Mac versão Xcode 7.0 ou posterior e Xamarin Studio comunidade instalado. Consulte [instalação e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [instalação, instalar e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](https://tryappservice.azure.com/?appServiceName=mobile). Você pode criar imediatamente um aplicativo móvel starter curta duração no aplicativo de serviço — sem cartão de crédito necessários e nenhum compromissos.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end do aplicativo do Azure Mobile

Siga estas etapas para criar um back-end do aplicativo Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o project server

Agora você tem provisionado um back-end do aplicativo do Azure Mobile que pode ser usado por seus aplicativos de cliente móvel. Em seguida, baixe um projeto de servidor para um simples "lista de tarefas pendentes" back-end e publicá-lo do Azure.

Siga estas etapas para configurar o projeto de servidor para usar o Node ou .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Baixar e executar o aplicativo de Xamarin.iOS

1. Abra o [portal do Azure] na janela do navegador.

2. Na lâmina configurações para o aplicativo móvel, clique em **Introdução** > **Xamarin.iOS**. Em etapa 3, clique em **criar um novo aplicativo** se ele ainda não estiver selecionado.  Em seguida, clique no botão **Download** .

    Um aplicativo cliente que se conecta ao seu back-end móvel é baixado. Salve o arquivo de projeto compactado em seu computador local e anote onde você salvá-lo.

3. Extrair o projeto que você baixou e abra-a no Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]

4. Pressione a tecla F5 para construir o projeto e iniciar o aplicativo no emulador iPhone.

5. No aplicativo, digite o texto significativo, como _Saiba Xamarin_e clique o **+** botão.

    ![][10]

    Dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo aplicativo móvel back-end e os dados são exibidos na lista.

>[AZURE.NOTE]Você pode examinar o código que acessa seu back-end do aplicativo móvel para consultar e inserir dados no arquivo QSTodoService.cs c#.

##<a name="next-steps"></a>Próximas etapas

* [Adicionar uma sincronização Offline para o seu aplicativo](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Adicione autenticação ao seu aplicativo](app-service-mobile-xamarin-ios-get-started-users.md)
* [Adicionar notificações por push para o seu aplicativo de Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Como usar o cliente gerenciado para aplicativos do Azure móvel](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
