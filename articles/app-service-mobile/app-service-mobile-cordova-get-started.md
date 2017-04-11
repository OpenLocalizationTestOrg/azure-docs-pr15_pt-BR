<properties
    pageTitle="Criar um aplicativo de Cordova em aplicativos móveis do serviço de aplicativo Azure | Microsoft Azure"
    description="Siga este tutorial para começar a usar usando o aplicativo móvel Azure back-ends para desenvolvimento de Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, cliente móvel, javascript," />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Criar um Apache Cordova aplicativo

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço baseado em nuvem back-end a um aplicativo móvel Apache Cordova usando um back-end do aplicativo móvel Azure.  Você criará um novo back-end do aplicativo móvel e uma simples _lista de Todo_ aplicativo Apache Cordova que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Apache Cordova sobre como usar o recurso de aplicativos Mobile no serviço de aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa do seguinte:

* Um PC com o [Visual Studio comunidade de 2015] ou mais recente.
* [Ferramentas do visual Studio para Apache Cordova].
* Uma [conta do Microsoft Azure active](https://azure.microsoft.com/pricing/free-trial/).

Você também pode ignorar o Visual Studio e usar a linha de comando Cordova Apache diretamente.  Isso é útil quando concluir o tutorial em um computador Mac.  Compilar aplicativos de cliente do Apache Cordova usando a linha de comando não é coberta por este tutorial.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end do Azure aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Assista a um vídeo mostrando etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Baixar e executar o aplicativo de Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial de início rápido, mova para um dos seguintes tutoriais:

* [Adicionar autenticação] ao seu Apache Cordova aplicativo.
* [Adicionar notificações por Push] para seu Apache Cordova aplicativo.

Saiba mais sobre conceitos-chave com o serviço de aplicativo do Azure.

* [Autenticação]
* [Notificações por push]

Saiba como usar o SDK do.

* [Apache Cordova SDK]
* [SDK do servidor do ASP.NET]
* [SDK do servidor Node]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Comunidade do Visual Studio de 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Adicionar autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar notificações por Push]: app-service-mobile-cordova-get-started-push.md
[Autenticação]: app-service-mobile-auth.md
[Notificações por push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do servidor do ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do servidor Node]: app-service-mobile-node-backend-how-to-use-server-sdk.md
