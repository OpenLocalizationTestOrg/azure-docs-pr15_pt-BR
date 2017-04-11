<properties
    pageTitle="Visão geral de mensagens do barramento de serviço | Microsoft Azure"
    description="Sistema de mensagens de barramento de serviço: entrega de dados flexível na nuvem"
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
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Barramento de serviço de mensagens: entrega de dados flexível na nuvem

Barramento de serviço do Microsoft Azure é um serviço de entrega de informações confiáveis. O objetivo desse serviço é facilitar a comunicação. Quando dois ou mais participantes desejam trocar informações, eles precisam de um mecanismo de comunicação. Barramento de serviço é um mecanismo de comunicação intermediário ou de terceiros. Isso é semelhante a um serviço postal no mundo físico. Serviços de postal tornam muito fácil enviar tipos diferentes de cartas e pacotes com uma variedade de garantias de entrega, em qualquer lugar do mundo.

Semelhante ao serviço postal oferecer letras, barramento de serviço é entrega flexível de informações do remetente e do destinatário. O serviço de mensagens garante que as informações são entregues mesmo se as duas partes nunca são os dois online ao mesmo tempo, ou se eles não estão disponíveis ao mesmo tempo exato. Dessa forma, de mensagens é semelhante ao envio de uma carta, enquanto comunicação orientada em não é semelhante a colocá uma chamada telefônica (ou como um telefonema costumava ser - antes de chamada em espera e chamador ID, que são muito mais como intermediário de mensagens).

O remetente da mensagem também pode exigir uma variedade de características de entrega, incluindo transações, detecção de duplicidades, expiração baseada em tempo e, em lotes. Esses padrões têm postais analogias também: Repetir entrega, assinatura necessária, alteração de endereço ou cancelamento.

Barramento de serviço oferece suporte a dois padrões de mensagens distintas: *retransmissão* e *comunicação de mensagens*.

## <a name="service-bus-relay"></a>Retransmissão de barramento de serviço

O componente de [retransmissão](../service-bus-relay/service-bus-relay-overview.md) do barramento de serviço é um serviço centralizado (mas altamente balanceamento de carga) que ofereça suporte a uma variedade de protocolos de transporte diferentes e padrões de serviços da Web. Isso inclui SOAP, WS-* e até mesmo restante. O [serviço de retransmissão](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) fornece uma variedade de opções de conectividade de retransmissão diferente e pode ajudar a negocie conexões ponto-a-ponto diretas quando possível. Barramento de serviço é otimizado para desenvolvedores de .NET que usam o Windows Communication Foundation (WCF), ambos relacionados ao desempenho e usabilidade e fornece acesso total ao seu serviço de retransmissão por meio de interfaces SOAP e restante. Isso possibilita qualquer SOAP ou restante ambiente de programação integrar o barramento de serviço.

O serviço de retransmissão suporta tradicional unidirecional de mensagens, mensagens de solicitação/resposta e ponto-ponto de mensagens. Ele também dá suporte à distribuição de evento em escopo de Internet para habilitar publicação assinatura cenários e comunicação bidirecional soquete para eficiência maior ponto a ponto. No padrão de mensagens de retransmissão, um serviço de local conecta o serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação vinculado a um endereço de reunião particular. O cliente, em seguida, pode se comunicar com o serviço de local enviando mensagens para o serviço de retransmissão direcionamento o endereço de reunião. O serviço de retransmissão será então "retransmissão" mensagens para o serviço de local por meio de soquete bidirecional já no lugar. O cliente não precisa de uma conexão direta para o serviço no local, nem é necessário saber onde o serviço reside e o serviço de local não precisa qualquer entradas portas abertas no firewall.

Iniciar a conexão entre o seu serviço local e o serviço de retransmissão, usando um conjunto de ligações de retransmissão"WCF". Nos bastidores, as associações de retransmissão mapeiam para elementos de ligação projetados para criar componentes de canal do WCF que se integram ao barramento de serviço na nuvem de transporte.

Retransmissão de barramento de serviço oferece muitos benefícios, mas exige que o servidor e o cliente para ambos estar online ao mesmo tempo para enviar e receber mensagens. Isso não é ideal para comunicação de estilo HTTP, na qual as solicitações podem não estar normalmente vida longa, nem para clientes que se conectam apenas ocasionalmente, como navegadores, aplicativos móveis e assim por diante. Orientado suporta desacoplado comunicação de mensagens e tem suas próprias vantagens; clientes e servidores podem se conectar quando necessário e executar as operações de maneira assíncrona.

## <a name="brokered-messaging"></a>Orientado a mensagens

Em contraste com o esquema de retransmissão, [comunicação de mensagens](service-bus-queues-topics-subscriptions.md) podem ser consideradas como assíncronas ou "no desassociado." Produtores (remetentes) e consumidores (receptores) não precisa estar online ao mesmo tempo. A infraestrutura de mensagens confiavelmente armazena mensagens em um agente de"" (como fila) até que o participante de consumo está pronto para recebê-los. Isso permite que os componentes do aplicativo distribuído seja desconectado, ou voluntariamente; Por exemplo, para manutenção ou devido a uma falha de componente, sem afetar todo o sistema. Além disso, o aplicativo de recebimento precise apenas ficar online durante determinadas horas do dia, como um sistema de gerenciamento de estoque que só é necessário para executar no final do expediente.

Os componentes principais da infraestrutura barramento de serviço intermediário mensagens são filas, tópicos e assinaturas.  A principal diferença é que tópicos suportam publicar/assinar os recursos que podem ser usados para sofisticados baseados em conteúdo roteamento e entrega lógica, incluindo enviando para vários destinatários. Esses componentes habilitam novos cenários de mensagens assíncronos, como separação temporal, publiquem/assinar e balanceamento de carga. Para saber mais sobre essas entidades mensagens, consulte [assinaturas, tópicos e filas de barramento de serviço](service-bus-queues-topics-subscriptions.md).

Como com a infraestrutura de retransmissão, o recurso de mensagens intermediário é fornecido para programadores WCF e .NET Framework e também via restante.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre mensagens de barramento de serviço, consulte os tópicos a seguir.

- [Fundamentos de barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Assinaturas, tópicos e filas de barramento de serviço](service-bus-queues-topics-subscriptions.md)
- [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como usar assinaturas e tópicos de barramento de serviço](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
