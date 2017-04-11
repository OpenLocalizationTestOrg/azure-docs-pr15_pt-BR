<properties
    pageTitle="Introdução ao conexões de híbrido de retransmissão | Microsoft Azure"
    description="Como escrever um aplicativo de console c# para conexões híbrido"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Introdução ao retransmissão híbrido conexões

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>O que será obtido

Como conexões híbrido requer um cliente e um componente de servidor, vamos criar dois aplicativos de console neste tutorial. Aqui estão as etapas:

1. Crie um namespace de retransmissão, usando o portal do Azure.

2. Crie uma Conexão de híbrido, usando o portal do Azure.

3. Escreva um servidor de aplicativo de console para receber mensagens.

4. Escreva um cliente de aplicativo de console para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Os exemplos deste tutorial usam Visual Studio 2015.

2. Uma assinatura do Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. criar um namespace usando o portal do Azure

Se você já tiver um namespace de retransmissão criado, ir para a seção [criar uma Conexão de híbrido usando o portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Crie uma Conexão de híbrido usando o portal do Azure

Se você já tiver uma Conexão de híbrido criado, ir para a seção de [criar um aplicativo de servidor](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. criar um aplicativo de servidor (ouvinte)

Para ouvir e receber mensagens de Relay, podemos irá escrever um aplicativo de console c# usando o Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. criar um aplicativo cliente (remetente)

Para enviar mensagens para a retransmissão, podemos irá escrever um aplicativo de console c# usando o Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. executar os aplicativos

1. Execute o aplicativo de servidor.

2. Executar o aplicativo cliente e digite algum texto.

3. Certifique-se de que o console de aplicativo do servidor saídas o texto que foi inserido no aplicativo cliente.

![execução de aplicativos](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, você criou um aplicativo de conexões híbrido de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas:

- [Retransmissão perguntas Frequentes](relay-faq.md)
- [Criar um namespace](relay-create-namespace-portal.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)