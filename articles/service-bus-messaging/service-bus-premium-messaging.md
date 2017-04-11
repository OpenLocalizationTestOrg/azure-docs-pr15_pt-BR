<properties
    pageTitle="Serviço barramento Premium e mensagens padrão visão geral de níveis de preços | Microsoft Azure"
    description="Serviço barramento Premium e sistema de mensagens padrão"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Serviço barramento Premium e níveis de mensagens padrão 

Barramento de serviço de mensagens, que inclui entidades como filas de mensagens e publiquem-assinar semântica em escala de nuvem de tópicos, enterprise combina recursos com rich de mensagens. Barramento de serviço de mensagens é usado como o backbone de comunicação muitas soluções de nuvem sofisticados.

O nível *Premium* de barramento de serviço de mensagens endereços solicitações de cliente comuns em torno de escala, desempenho e disponibilidade para aplicativos essenciais. Embora os conjuntos de recursos sejam quase idênticos, esses dois níveis de barramento de serviço de mensagens foram projetados para servir diferentes casos de uso.

Algumas diferenças de alto nível são realçadas na tabela abaixo.

| Premium                               | Padrão                       |
|---------------------------------------|--------------------------------|
| Alta taxa de transferência                       | Produtividade variável            |
| Desempenho previsível               | Latência variável               |
| Preços previsíveis                   | Pré-pago variável preços |
| Capacidade de expansão para carga de trabalho | N/D                            |
| > 256KB de tamanho de mensagem                  | Tamanho da mensagem é 256KB          |

**Mensagens do serviço barramento Premium** fornece isolamento de recurso na camada de CPU e memória para que cada carga de trabalho do cliente é executado em isolamento. Este contêiner de recurso é chamado de uma *unidade de mensagens*. Cada namespace premium é alocado pelo menos uma unidade de mensagens. Você pode adquirir 1, 2 ou 4 unidades mensagens para cada namespace Premium de barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora seja cobrança em tarifas de taxa de 24 horas ou diariamente. O resultado é desempenho previsível e repetitivo para sua solução baseada em barramento de serviço.

Não só é esse desempenho mais previsíveis e disponível, mas também é mais rápido. Mensagens de serviço barramento Premium amplia o mecanismo de armazenamento introduzido em [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/). Com as mensagens Premium, desempenho de pico é muito mais rápido do que com a camada padrão.

## <a name="premium-messaging-technical-differences"></a>Diferenças de técnicas de mensagens Premium

A seguir estão algumas diferenças entre Premium e níveis de mensagens padrão.

### <a name="partitioned-queues-and-topics"></a>Tópicos e filas particionadas

Tópicos e filas particionadas são suportados no Premium de mensagens, mas elas não funcionar da mesma maneira como os níveis padrão e Basic de barramento de serviço de mensagens. Premium de mensagens não usa SQL como armazenamento de dados e já não tem a concorrência possível recurso associado uma plataforma compartilhada. Como resultado, não é necessário partição. Além disso, a contagem de partição foi alterada de 16 partições no sistema de mensagens padrão para 2 partições no Premium. Ter duas partições garante a disponibilidade e é um número mais apropriado para o ambiente de runtime Premium. Para obter mais informações sobre partição, consulte [tópicos e filas particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Express entidades

Como Premium de mensagens é executado em um ambiente de tempo de execução completamente isolados, entidades express não são suportadas nos namespaces Premium. Para obter mais informações sobre o recurso express, consulte a propriedade [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre mensagens de barramento de serviço, consulte os tópicos a seguir.

- [Introdução ao Azure serviço barramento Premium (postagem de blog) de mensagens](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução ao Azure serviço barramento Premium (Channel9) de mensagens](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Visão geral de mensagens do barramento de serviço](service-bus-messaging-overview.md)
- [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
