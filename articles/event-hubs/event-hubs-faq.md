<properties 
    pageTitle="Hubs de evento perguntas frequentes (FAQ) | Microsoft Azure"
    description="Hubs de evento perguntas Frequentes."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Hubs de evento perguntas Frequentes

Hubs de evento fornece ar grande escala, persistência e processamento de eventos de dados de fontes de dados de alta produtividade e/ou milhões de dispositivos. Quando combinado com tópicos e filas de barramento de serviço, Hubs de evento permite implantações de comando e controle persistentes para cenários de [Internet das coisas (IoT)](https://azure.microsoft.com/services/iot-hub/) .

Este artigo discute informações sobre preços e responde a algumas perguntas frequentes sobre o evento Hubs:

## <a name="pricing-information"></a>Informações sobre preços

Para obter informações completas sobre os preços de Hubs de evento, consulte os [detalhes de preços Hubs de evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Como os eventos de ingresso Hubs de evento são calculados?

Cada evento enviado a um Hub de evento conta como uma mensagem faturável. Um *evento de ingresso* é definido como uma unidade de dados que é menor ou igual a 64 KB. Qualquer evento que é menor ou igual a 64 KB em tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos faturáveis é calculado de acordo com o tamanho de evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o Hub de evento será cobrado como um evento, mas uma mensagem de 96 KB enviada para o Hub de evento será cobrada como dois eventos.

Eventos consumidos de um Hub de evento, como bem como operações de gerenciamento e controlam chamadas como pontos de verificação, não são contados como eventos de ingresso faturáveis, mas acumulam até a reserva de unidade de produtividade.

## <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produtividade Hubs de eventos?

Você seleciona explicitamente unidades de produtividade Hubs de evento, através do portal do Azure ou Hubs de evento recurso Gerenciador de modelos. Unidades de produtividade se aplicam a todos os Hubs de evento em um namespace Hubs de evento, e cada unidade de produtividade autoriza o namespace para os seguintes recursos:

- Para cima para 1 MB por segundo dos eventos de ingresso (eventos enviados a um Hub de evento), mas não há mais de 1000 eventos de ingresso, operações de gerenciamento ou controle chamadas API por segundo.

- Até 2 MB por segundo de eventos de saída (eventos consumidos de um Hub de evento).

- Até 84 GB de armazenamento de evento (suficiente para o período de retenção de 24 horas padrão).

Unidades de produtividade de Hubs de evento serão cobradas por hora, com base no número máximo de unidades selecionadas durante a determinada hora.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Como os limites de unidade de produtividade Hubs de evento são aplicados?

Se a taxa de transferência de ingresso total ou a taxa de eventos de ingresso total em todos os Hubs de evento em um namespace excede o permitido de unidade de transferência agregada, remetentes serão reduzidos e recebem erros indicando que a cota de ingresso foi excedida.

Se a taxa de transferência de saída total ou a taxa de egresso de evento total em todos os Hubs de evento em um namespace excede o permitido de unidade de transferência agregada, receptores são resolvidos e recebem erros indicando que a cota de egresso foi excedida. As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente pode causar consumo de evento lento, nem pode um receptor impedir eventos sendo enviado a um Hub de evento.

Observe que a seleção de unidade de produtividade é independente do número de partições Hubs de evento. Enquanto cada partição oferece uma taxa de transferência máxima de 1 MB por segundo ingresso (com um máximo de 1000 eventos por segundo) e 2 MB por segundo egresso, não há nenhum encargo fixo para as partições próprios. O encargo é para as unidades de produtividade agregado em todos os Hubs de evento em um namespace Hubs de evento. Com esse padrão, você pode criar suficiente partições para oferecer suporte a carga máxima prevista para seus sistemas, sem cobrado qualquer produtividade unidade até que a carga de evento no sistema realmente exige números maiores de produtividade e, sem ter de alterar a estrutura e arquitetura de sistemas como a carga no sistema aumenta.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Existe um limite no número de unidades de produtividade que podem ser selecionados?

Não há uma cota padrão de 20 unidades de produtividade por namespace. Você pode solicitar uma cota maior de unidades de produtividade arquivando um tíquete. Fora do limite de unidade de 20 produtividade, estão disponíveis em 20 e 100 unidades de produtividade pacotes. Observe que usando mais de 20 unidades de produtividade remove a capacidade de alterar o número de unidades de produtividade sem um tíquete de arquivamento.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe um custo para reter eventos de Hubs de evento para mais de 24 horas?

A camada de evento Hubs padrão permitir retenção de mensagens períodos mais de 24 horas, um máximo de 30 dias. Se o tamanho do número total de eventos armazenados exceder a reserva de armazenamento para o número de unidades de produtividade selecionado (84 GB por unidade de produtividade), o tamanho que excede a reserva cobrada de acordo com a taxa de armazenamento de Blob do Azure publicada. A reserva de armazenamento em cada unidade de produtividade abrange todos os custos de armazenamento períodos de retenção de 24 horas (padrão) mesmo se a unidade de produtividade é usada para cima para a reserva de ingresso máximo.

## <a name="what-is-the-maximum-retention-period"></a>O que é o período de retenção máximo?

Nível de Hubs padrão de evento atualmente oferece suporte a um período de retenção máximo de 7 dias. Observe que Hubs de evento não destinam-se como um armazenamento de dados permanentes. Períodos de retenção maiores do que 24 horas são destinados a cenários em que é conveniente reproduzir um fluxo de evento para os mesmos sistemas; Por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina em dados existentes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é o tamanho do armazenamento de evento Hubs calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo qualquer sobrecarga interna para cabeçalhos de eventos ou em estruturas de armazenamento de disco em todos os Hubs de evento, é medido durante o dia. No final do dia, o tamanho do armazenamento de pico é calculado. A reserva de armazenamento diárias é calculada com base no número mínimo de unidades de produtividade que foram selecionados durante o dia (cada unidade de produtividade fornece uma reserva de 84 GB). Se o tamanho total exceder a reserva de armazenamento diárias calculado, o armazenamento em excesso é cobrado usando as taxas de armazenamento de Blob do Azure (com a taxa de **Armazenamento redundante localmente** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>Pode usar uma conexão de AMQP única para enviar e receber de Hubs de evento e tópicos da filas barramento de serviço?

Sim, desde que todos os eventos Hubs, filas e tópicos são no mesmo namespace. Assim, você pode implementar conectividade bidirecional, comunicação com muitos dispositivos, com latências subsecond, de maneira econômica e altamente escalável.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Encargos de conexão intermediário são aplicáveis a Hubs de eventos?

Remetentes, encargos de conexão se aplicam apenas quando o protocolo AMQP é usado. Não há nenhum encargos de conexão para o envio de eventos usando HTTP, independentemente do número de envio de sistemas ou dispositivos. Se você planeja usar AMQP (por exemplo, para obter mais eficiente streaming de evento ou habilitar a comunicação bidirecional em cenários de comando e controle de IoT), consulte a página de [informações sobre preços barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/) para obter informações sobre o que constitui uma conexão de comunicação e como eles são limitados.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre o evento Hubs básicas e níveis padrão?

Nível de Hubs padrão de evento fornece recursos além do que é disponível em evento Hubs básica e em alguns sistemas competitivos. Esses recursos incluem períodos de retenção de mais de 24 horas e a capacidade de usar uma conexão de AMQP única para enviar comandos para um grande número de dispositivos com latências subsecond, bem como para enviar telemetria nesses dispositivos em Hubs de evento. Para a lista de recursos, consulte os [detalhes de preços Hubs de evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilidade geográfica

Hubs de evento está disponível nas regiões a seguir:

|Localização geográfica|Regiões|
|---|---|
|Estados Unidos|Centro dos EUA, Leste EUA, Leste dos EUA 2, Centro Sul dos EUA, oeste EUA|
|Europa|Europa do Norte, Europa Ocidental|
|Pacífico|Leste Asiático, Sudeste Asiático|
|Japão|Japão Leste Japão Oeste|
|Brasil|Brasil Sul|
|Austrália|Austrália Oriental, Austrália Sudeste|

## <a name="support-and-sla"></a>Suporte e SLA

Suporte técnico para Hubs de evento está disponível por meio de [Fóruns da comunidade](https://social.msdn.microsoft.com/forums/azure/home). Suporte de gerenciamento de cobrança e assinatura é fornecido sem custo.

Para saber mais sobre nosso SLA, consulte a página de [Contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de evento, consulte os seguintes artigos:

- [Visão geral de Hubs de evento][].
- Um [aplicativo de exemplo que usa o evento Hubs][]completo.

[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
