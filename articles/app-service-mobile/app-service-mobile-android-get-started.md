<properties
    pageTitle="Criar um aplicativo do Android em aplicativos móveis do serviço de aplicativo Azure | Microsoft Azure"
    description="Siga este tutorial para começar a usar usando o aplicativo móvel Azure back-ends para desenvolvimento Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Criar um aplicativo do Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço baseado em nuvem back-end a um aplicativo móvel do Android, usando um back-end do aplicativo móvel Azure.  Você criará um novo back-end do aplicativo móvel e uma simples _lista de tarefas pendentes_ aplicativos Android que armazena dados de aplicativo no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Android sobre como usar o recurso de aplicativos Mobile no serviço de aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa do seguinte:

* [Ferramentas de desenvolvedor do android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma mais recente do Android.
* Azure celular Android SDK, que é referenciada automaticamente como parte do projeto início rápido que você baixar.
* Uma [conta do Microsoft Azure active](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end do Azure aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Baixar e executar o aplicativo do Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
