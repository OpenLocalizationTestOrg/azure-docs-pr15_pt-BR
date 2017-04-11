<properties
    pageTitle="Criar um namespace barramento de serviço no portal Azure | Microsoft Azure"
    description="Para começar a usar barramento de serviço, você precisará de um namespace. Veja aqui como criar uma usando o portal do Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um namespace barramento de serviço usando o portal do Azure

Um namespace é um contêiner comum de todos os componentes de mensagens. Vários tópicos e filas podem residem em um único namespace e namespaces geralmente servir como contêineres de aplicativo. Há duas maneiras diferentes para criar um namespace barramento de serviço.

1.  Portal do Azure (neste artigo)

2.  [Modelos do Gerenciador de recursos][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um namespace no portal do Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora que você criou um namespace barramento de serviço de mensagens.

## <a name="next-steps"></a>Próximas etapas

Confira nossos [exemplos GitHub] [ github-samples] que mostram alguns dos recursos mais avançados do Azure barramento de serviço de mensagens.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples