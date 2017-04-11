<properties
    pageTitle="Visão geral de retransmissão de barramento de serviço | Microsoft Azure"
    description="Visão geral de retransmissão de barramento de serviço."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Visão geral de retransmissão de barramento de serviço

Um componente principal do barramento de serviço é um serviço de *retransmissão* de centralizado (mas altamente balanceamento de carga) que permite que você crie aplicativos híbridos que executar em um data center Azure e seu próprio ambiente de empresa local.  A retransmissão de barramento de serviço oferece suporte a uma variedade de protocolos de transporte diferentes e padrões de serviços da web. Isso inclui SOAP, WS-* e até mesmo restante. O serviço de retransmissão facilita seus aplicativos híbridos habilitando com segurança expor os serviços do Windows Communication Foundation (WCF) que residem dentro de uma rede corporativos para a nuvem pública, sem precisar abrir um conexão de firewall, ou exigem mudanças intrusivas uma infraestrutura de rede corporativa. 

![Conceitos de retransmissão](./media/service-bus-relay-overview/sb-relay-01.png)

O serviço de retransmissão suporta tradicional unidirecional de mensagens, mensagens de solicitação/resposta e ponto-ponto de mensagens. Ele também dá suporte à distribuição de evento em escopo de internet para habilitar cenários publicar/assinar e comunicação bidirecional soquete para eficiência maior ponto a ponto. 

No padrão de mensagens de retransmissão, um serviço de local conecta o serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação vinculado a um endereço de reunião particular. O cliente, em seguida, pode se comunicar com o serviço de local enviando mensagens para o serviço de retransmissão direcionamento o endereço de reunião. O serviço de retransmissão será então "retransmissão" mensagens para o serviço de local por meio de soquete bidirecional já no lugar. O cliente não precisa de uma conexão direta para o serviço no local, não é necessário saber onde o serviço reside e o serviço de local não precisa qualquer entradas portas abertas no firewall.

Você pode iniciar a conexão entre o seu serviço no local e o serviço de retransmissão usando um conjunto de ligações de retransmissão"WCF". Nos bastidores, as associações de retransmissão mapeiam para novos elementos de ligação de transporte projetados para criar componentes de canal do WCF que se integram ao barramento de serviço na nuvem. 

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre a retransmissão de barramento de serviço, consulte os tópicos a seguir.

- [Visão geral da arquitetura do serviço Azure barramento](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Como usar o serviço de retransmissão de barramento de serviço](service-bus-dotnet-how-to-use-relay.md)

 