<properties 
    pageTitle="Barramento de serviço preços e cobrança | Microsoft Azure"
    description="Visão geral da estrutura de preços de barramento de serviço."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Barramento de serviço preços e cobrança

Barramento de serviço é oferecido em níveis Basic, Standard e [Premium](service-bus-premium-messaging.md) . Você pode escolher um nível de serviço para cada namespace barramento de serviço do serviço que você cria e esta seleção camada aplica em todas as entidades criadas dentro desse namespace.

>[AZURE.NOTE] Para obter informações detalhadas sobre os preços do atual barramento de serviço, consulte a [página de preços de barramento de serviço do Azure](https://azure.microsoft.com/pricing/details/service-bus/)e as [Perguntas frequentes sobre o barramento de serviço](service-bus-faq.md#service-bus-pricing).

Barramento de serviço usa os seguintes medidores dois para filas tópicos/assinaturas:

1. **Operações de mensagens**: definido como chamadas de API contra fila ou tópico/assinatura pontos de extremidade do serviço. Esse medidor substituirão mensagens enviadas ou recebidas como a unidade principal de utilização faturável para filas e tópicos/assinaturas.

2. **Conexões orientado**: definido como o número máximo de conexões persistentes abre contra filas, tópicos ou assinaturas durante um período determinado amostragem de uma hora. Esse medidor serão aplicadas somente na camada padrão, na qual você pode abrir conexões adicionais (anteriormente, conexões foram limitados a 100 por fila/tópico/assinatura) para uma taxa de conexão de nominal.

A camada **padrão** introduz preços gradual para operações executadas com filas e tópicos/assinaturas, resultando em baseado em volume descontos de até 80% altos níveis de uso. Também há um encargo base de nível padrão de US $10 por mês, que permite executar operações de até 12.5 milhões por mês sem nenhum custo adicional.

A camada de **Premium** fornece isolamento de recurso na camada de CPU e memória para que cada carga de trabalho do cliente é executado em isolamento. Este contêiner de recurso é chamado de uma *unidade de mensagens*. Cada namespace premium é alocado pelo menos uma unidade de mensagens. Você pode adquirir 1, 2 ou 4 unidades mensagens para cada namespace Premium de barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora seja cobrança em tarifas de taxa de 24 horas ou diariamente. O resultado é desempenho previsível e repetitivo para sua solução baseada em barramento de serviço. Não só é esse desempenho mais previsíveis e disponível, mas também é mais rápido. Mensagens de serviço barramento Premium Azure baseia o mecanismo de armazenamento introduzido em Hubs de evento do Azure. Com as mensagens Premium, o desempenho de pico é muito mais rápido que a camada padrão.

Observe que o encargo base padrão é cobrado apenas uma vez por mês por assinatura do Azure. Isso significa que depois de criar um único padrão ou namespace de barramento de serviço de nível Premium, você será capaz de criar quantas Standard ou Premium camada namespaces adicionais desejar nessa mesma assinatura do Azure, sem cobrado base adicionais.

Todos os namespaces barramento de serviço existentes criados anteriores a 1 de novembro de 2014 foram colocados automaticamente na camada de padrão. Isso garante que você continue a ter acesso a todos os recursos disponíveis atualmente com barramento de serviço. Posteriormente, você pode usar o [Azure portal clássico][] de downgrade para o nível básico se desejado.

A tabela a seguir resume as diferenças funcionais entre os níveis básico e padrão/Premium.

|Recurso|Básico|Padrão/Premium|
|---|---|---|
|Filas|Sim|Sim|
|Mensagens agendadas|Sim|Sim|
|Tópicos/assinaturas|Não|Sim|
|Retransmissões|Não|Sim|
|Transações|Não|Sim|
|Duplicação|Não|Sim|
|Sessões|Não|Sim|
|Mensagens grandes|Não|Sim|
|ForwardTo|Não|Sim|
|SendVia|Não|Sim|
|Conexões de comunicação (incluídos)|100 por namespace barramento de serviço|1.000 por assinatura do Azure|
|Conexões de comunicação (excedente permitida)|Não|Sim (Faturável)|

## <a name="messaging-operations"></a>Operações de mensagens

Como parte do novo modelo de preços, cobrança para filas tópicos/assinaturas está mudando. Essas entidades estão migrando de cobrança por mensagem para cobrança por operação. Uma operação de"" se refere a qualquer chamada de API feita em relação a um ponto de extremidade de fila ou tópico/assinatura de serviço. Isso inclui operações de estado de gerenciamento, enviar/receber e sessão.

|Tipo de operação|Descrição|
|---|---|
|Gerenciamento|Crie, ler, atualização, exclusão (CRUD) contra filas ou tópicos/assinaturas.|
|Sistema de mensagens|Enviando e recebendo mensagens com filas ou tópicos/assinaturas.|
|Estado de sessão|Obter ou definir o estado de sessão em uma fila ou tópico/assinatura.|

Os seguintes preços foram eficazes começando em 1 de novembro de 2014:

|Básico|Custo|
|---|---|
|Operações|US $0,05 por milhões operações|

|Padrão|Custo|
|---|---|
|Encargo base|$10/ mês|
|Primeiro milhões de 12.5 operações/mês|Incluído|
|12.5 operações de-100 milhões/mês|$0,80 por milhões operações|
|operações de 100 milhões - 2.500 milhões/mês|US $0,50 por milhões operações|
|Operações de mais de 2.500 milhões/mês|US $0,20 por milhões operações|

|Premium|Custo|
|---|---|
|Diárias|$11.13 fixo taxa por unidade de mensagem|

## <a name="brokered-connections"></a>Conexões de comunicação

*Conexões de Brokered* acomodar padrões de uso do cliente que envolvem um grande número de "constantemente conectados" remetentes/receptores contra filas, tópicos ou assinaturas. Remetentes/receptores constantemente conectados são aqueles que se conectam usando AMQP ou HTTP com uma diferente de zero recebem tempo limite (por exemplo, HTTP longa de sondagem). HTTP remetentes e receptores com um tempo limite imediato não geram conexões de comunicação.

Anteriormente, filas e tópicos/assinaturas tinham um limite de 100 conexões simultâneas por URL. O esquema de cobrança atual remove o limite por-URL para filas e tópicos/assinaturas e implementa cotas e monitoração em conexões de comunicação nos níveis de assinatura Azure e namespace barramento de serviço.

A camada básica inclui e terminantemente limita a 100 conexões intermediário por namespace barramento de serviço. Conexões acima esse número serão rejeitadas na camada básica. A camada padrão remove o limite por namespace e contagens de uso de agregação de conexão de comunicação entre a assinatura do Azure. Na camada padrão, 1.000 conexões intermediário por assinatura Azure poderão ser sem custo (além do encargo base) adicional. Usando mais de um total de 1.000 conexões de comunicação em barramento de serviço do padrão camadas namespaces em uma assinatura do Azure serão cobrados no cronograma de um gradiente, conforme mostrado na tabela a seguir.

|Conexões de comunicação (camada padrão)|Custo|
|---|---|
|Primeiro 1.000/mês|Incluído no encargo base|
|1.000-100,000/mês|$0.03 por conexão/mês|
|100,000-500.000/mês|$0.025 por conexão/mês|
|500.000/mês|$0.015 por conexão/mês|

>[AZURE.NOTE] 1.000 conexões de comunicação são fornecidos com a camada de mensagens padrão (via o encargo base) e podem ser compartilhados entre todas as filas, tópicos e assinaturas na assinatura Azure associado.

<br />

>[AZURE.NOTE] Cobrança baseia-se o número máximo de conexões simultâneas e rateada por hora com base em 744 horas por mês.

|Nível de Premium
|---|
|Conexões de comunicação não são cobrados na camada Premium.|

Para obter mais informações sobre conexões de comunicação, consulte a seção [perguntas Frequentes](#faq) mais adiante neste tópico.

## <a name="relay"></a>Retransmissão

Retransmissões estão disponíveis apenas no nível padrão namespaces. Caso contrário, as cotas de preços e conexão retransmissões permanecem inalteradas. Isso significa que retransmissões continuará a ser cobrado no número de mensagens (não operações) e horas de retransmissão.

|Retransmissão preços|Custo|
|---|---|
|Horas de retransmissão|US $0,10 para cada horas de 100 retransmissão|
|Mensagens|US $0,01 para cada 10.000 mensagens|

## <a name="faq"></a>Perguntas Freqüentes

### <a name="how-is-the-relay-hours-meter-calculated"></a>Como é calculado o medidor de retransmissão horas?

Consulte [Este tópico](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>O que são orientado conexões e como posso obter cobrado por elas?

Uma conexão de comunicação é definido como um destes procedimentos:

1. Uma conexão de AMQP de um cliente para um fila de barramento de serviço ou tópico/assinatura.

2. Uma chamada HTTP para receber uma mensagem de um tópico de barramento de serviço ou fila que tem um valor de tempo limite de recebimento maior que zero.

Encargos de barramento de serviço para o número máximo de conexões simultâneas de comunicação que exceder a quantidade incluída (1.000 na camada padrão). Picos são medidos em uma base por hora, rateados dividindo por 744 horas em um mês e somados ao longo do período de faturamento mensal. A quantidade de incluído (1.000 conexões intermediário por mês) é aplicada no final do período de faturamento em relação a soma dos rateado picos por hora.

Por exemplo:

1. Cada um dos 10.000 dispositivos se conecta por meio de uma única conexão AMQP e recebe comandos de um tópico de barramento de serviço. Os dispositivos enviar eventos de telemetria a um Hub de evento. Se todos os dispositivos se conectar por 12 horas por dia, os encargos de conexão a seguir se aplicam (além de quaisquer outros encargos de tópico de barramento de serviço): 10.000 conexões *12 horas* 31 dias / 744 = 5.000 orientado conexões. Após a reserva mensal de 1.000 conexões de comunicação, você seria cobrada de 4.000 conexões de comunicação, em uma taxa de US $0.03 por conexão de comunicação, para um total de US $120.

2. 10.000 dispositivos recebem mensagens de uma fila de barramento de serviço por meio de HTTP, especificando um tempo limite diferente de zero. Se todos os dispositivos se conectar por 12 horas diariamente, você verá os encargos de conexão seguinte (além de quaisquer outros encargos barramento de serviço): 10.000 HTTP receber conexões *12 horas por dia* 31 dias / horas 744 = 5.000 orientado conexões.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Cobrados encargos de conexão intermediário filas e tópicos/assinaturas?

Sim. Não há nenhum encargos de conexão para o envio de eventos usando HTTP, independentemente do número de envio de sistemas ou dispositivos. Quando você receber eventos com HTTP usando um tempo limite maior que zero, às vezes chamado de "sondagem longa," gera encargos de conexão intermediário. Conexões de AMQP geram encargos de conexão de comunicação, independentemente se as conexões estão sendo usadas para enviar ou receber. Observe que 100 conexões de comunicação são permitidas sem nenhum custo em um namespace básico. Isso também é o número máximo de conexões de comunicação permitidos para a assinatura do Azure. As primeiro 1.000 conexões de comunicação entre todos os namespaces padrão em uma assinatura do Azure são incluídas sem nenhum custo adicional (além do encargo base). Porque esses permitido é suficientes para cobrir o muitos cenários de mensagens ao serviço, encargos de conexão de comunicação geralmente somente se tornam relevantes se você planeja usar sondagem longa AMQP ou HTTP com um grande número de clientes; Por exemplo, para obter mais eficiente streaming de evento ou habilitar a comunicação bidirecional com muitos dispositivos ou instâncias do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais detalhes sobre os preços de barramento de serviço, consulte a [página de preços de barramento de serviço do Azure](https://azure.microsoft.com/pricing/details/service-bus/).

- Consulte as [Perguntas frequentes sobre o barramento de serviço](service-bus-faq.md#service-bus-pricing) para alguns frequentes em torno de barramento de serviço preços e cobrança.

[Azure portal clássico]: http://manage.windowsazure.com