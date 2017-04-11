<properties 
    pageTitle="Perguntas Frequentes de retransmissão | Microsoft Azure"
    description="Responde a algumas perguntas frequentes sobre o Azure retransmissão."
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager=""
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-faq"></a>Retransmissão perguntas Frequentes

Este artigo responde algumas perguntas frequentes sobre o Microsoft Azure retransmissão. Você também pode visitar o [Perguntas Frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais de suporte e preços Azure. Os tópicos a seguir estão incluídos:

- [Perguntas gerais sobre o Azure Relay](#general-questions)
- [Preços](#pricing)
- [Cotas](#quotas)
- [Gerenciamento de assinatura e namespace](#subscription-and-namespace-management)
- [Solução de problemas](#troubleshooting)

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-azure-relay"></a>O que é Azure retransmissão?

O [serviço de retransmissão](relay-what-is-it.md) fornece a capacidade de hospedar transparente e acessar serviços WCF em qualquer lugar. Em outras palavras, isso permite que aplicativos híbridos que executar em um data center Azure e um ambiente de empresa local.

### <a name="what-is-a-relay-namespace"></a>O que é um namespace de retransmissão?

Um [namespace](Relay-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos de retransmissão dentro de seu aplicativo. Criando um é necessário usar retransmissão e será uma das primeiras etapas na introdução.

## <a name="pricing"></a>Preços

Esta seção responde algumas perguntas frequentes sobre a estrutura de preços de retransmissão. Você também pode visitar o [Azure perguntas frequentes de suporte](http://go.microsoft.com/fwlink/?LinkID=185083) para obter informações sobre preços Microsoft Azure gerais. Para obter informações completas sobre preços de retransmissão, consulte [detalhes de preços de barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-relay"></a>Como você cobra para retransmissão?

Para obter informações completas sobre os preços de retransmissão, consulte [detalhes de preços de barramento de serviço][Pricing overview]. Além dos preços observados, cobrado para transferências de dados associados de egresso fora do data center no qual seu aplicativo está provisionado.

### <a name="what-usage-of-relay-is-subject-to-data-transfer"></a>Quais são os usos de retransmissão estão sujeito a transferência de dados?

Retransmissão inclui 5 GB de entrada de dados por mês, por assinatura. Há nenhuma cobrança de ingresso/egresso Azure adicionais para usado pelo retransmissão de dados.

A carga de dados de retransmissão para entrada de remetentes somente, como retransmissão ouvintes não aumentam a carga de dados. Por exemplo, se você enviar 1 GB, você só será cobrado para 1 GB, embora um ouvinte também recebido 1 GB e pode ser fora dos data centers do Azure.

### <a name="how-are-relay-hours-calculated"></a>Como as horas de retransmissão são calculadas?

Retransmissão horas são faturadas para o valor acumulado de tempo durante o qual cada Relay é "aberto" durante um determinado período de cobrança. Uma retransmissão é implicitamente criada e aberta em um determinado namespace quando um ouvinte de retransmissão primeiro se conecta a esse endereço. A retransmissão é fechada somente quando o ouvinte último desconecta de seu endereço. Portanto, para fins de faturamento que uma retransmissão é considerada "aberta" do horário o ouvinte de retransmissão primeiro se conecta, o tempo que o ouvinte de retransmissão último desconecta do namespace. Em outras palavras, uma retransmissão é considerada aberta sempre que um ou mais ouvintes de retransmissão estiver conectados ao seu endereço de barramento de serviço.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>E se eu tiver mais de um ouvinte conectado a uma determinada retransmissão?

Em alguns casos, uma única retransmissão pode ter vários ouvintes conectados. Uma retransmissão é considerada "abrir" quando pelo menos um ouvinte de retransmissão está conectado a ela. Adicionando ouvintes adicionais para uma retransmissão aberta resultará em horas de retransmissão adicionais. O número de remetentes de retransmissão (clientes que invocar ou enviam mensagens para retransmissões) conectada a uma retransmissão também não tem efeito sobre o cálculo das horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como o medidor de mensagens é calculado retransmissões WCF?

**Isso só é aplicável para retransmissões WCF e não é um custo de conexões de híbrido**

Em geral, mensagens faturáveis são calculadas retransmissões usando o mesmo método conforme descrito acima para comunicação entidades (filas, tópicos e assinaturas). No entanto, há várias diferenças notáveis:

Enviar que uma mensagem para uma retransmissão de barramento de serviço será tratada como um "completo por meio de" envia para o ouvinte de retransmissão que recebe a mensagem, em vez de um enviar para a retransmissão de barramento de serviço seguido por uma entrega para o ouvinte de retransmissão. Portanto, uma invocação de serviço de estilo de solicitação-resposta (de até 64 KB) em relação a um ouvinte de retransmissão resultará em duas mensagens faturáveis: uma única mensagem faturável para a solicitação e uma única mensagem faturável para a resposta (presumindo que a resposta também é \<= 64 KB). Isso difere do usando uma fila para intermediário entre um cliente e um serviço. No último caso, o mesmo padrão de solicitação-resposta precisaria enviar uma solicitação para a fila, seguido por uma dequeue/entrega da fila para o serviço, seguido de enviar uma resposta para outra fila e uma dequeue/entrega dessa fila para o cliente. Usando a mesma (\<= 64 KB) Pressuposições de tamanho em todo, o padrão de fila mediada assim resultaria em quatro mensagens faturáveis, o dobro de cobrado para implementar o mesmo padrão usando retransmissão. Claro, há benefícios usando filas para atingir esse padrão, como durabilidade e carregar nivelamento. Esses benefícios podem justificar as despesas adicionais.

Retransmissores abertos usando a ligação de WCF netTCPRelay tratam mensagens não como mensagens individuais, mas como um fluxo de dados fluindo através do sistema. Em outras palavras, somente o remetente e o ouvinte têm visibilidade o enquadramento do indivíduo mensagens enviadas/recebido usando essa ligação. Portanto, retransmissões usando a ligação netTCPRelay, todos os dados será tratado como um fluxo para calcular faturáveis mensagens. Nesse caso, barramento de serviço calculará a quantidade total de dados enviada ou recebida através de cada retransmissão individual em uma base de 5 minutos e pelo qual dividir desse total 64 KB para determinar o número de mensagens faturáveis para a retransmissão em questão durante esse período de tempo.

## <a name="quotas"></a>Cotas

|Nome de cota|Escopo|Tipo|Comportamento quando excedida|Valor|
|---|---|---|---|---|
|Ouvintes simultâneas em um relay|Entidade|Estático|As solicitações subsequentes para conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.|25|
|Ouvintes de retransmissão simultâneas|Todo o sistema|Estático|As solicitações subsequentes para conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.|2.000|
|Conexões de retransmissão simultâneas por todos os pontos de extremidade de retransmissão em um namespace de serviço|Todo o sistema|Estático|-|5.000|
|Pontos de extremidade de retransmissão por namespace do serviço|Todo o sistema|Estático|-|10.000|
|Tamanho da mensagem retransmissões [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx)|Todo o sistema|Estático|As mensagens de entrada que excedeu essas cotas serão rejeitadas e uma exceção será recebida pelo código de chamada.|64KB
|Tamanho da mensagem retransmissões [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)|Todo o sistema|Estático|-|Ilimitado|

### <a name="does-relay-have-any-usage-quotas"></a>Retransmissão tem as cotas de uso?

Por padrão, para qualquer nuvem serviço Microsoft define uma cota de uso mensal agregada que é calculada em todas as assinaturas de um cliente. Porque entendemos que talvez seja necessário mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos compreender suas necessidades e ajustar esses limites adequadamente. Para barramento de serviço, as cotas de uso agregadas são da seguinte maneira:

- 5 bilhões de mensagens
- horas de retransmissão de 2 milhões

Enquanto podemos reservar à direita para desabilitar uma conta de cliente que excedeu seus uso cotas em um determinado mês, podemos fornecerá notificação por email e fazer várias tentativas de contato com um cliente antes de realizar qualquer ação. Clientes ultrapassando essas cotas ainda será responsáveis por encargos que excederem as cotas.

#### <a name="naming-restrictions"></a>Restrições de nomenclatura

Um nome de namespace de retransmissão só pode estar entre 6-50 caracteres de comprimento.

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinatura e namespace

### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como migrar um namespace para outra assinatura do Azure?

Você pode usar os comandos do PowerShell (encontrados no artigo [aqui](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) para mover um namespace de uma assinatura do Azure para outro. Para executar a operação, namespace já deve estar ativo. Também o usuário execução dos comandos deve ser um administrador no assinaturas de origem e de destino.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas por APIs de retransmissão do Azure e suas ações sugeridas?

As [exceções de retransmissão] [ Relay exceptions] artigo descreve algumas exceções com as ações sugeridas.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma assinatura de acesso compartilhado e quais idiomas oferece suporte para gerar uma assinatura?

Assinaturas de acesso compartilhado são um mecanismo de autenticação com base em SHA – 256 hash seguro ou URIs. Para obter informações sobre como gerar seus próprio assinaturas no nó, PHP, Java e C\#, consulte o artigo de [Assinaturas de acesso compartilhado][] .

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Assinaturas de acesso compartilhado]: service-bus-sas-overview.md

## <a name="next-steps"></a>Próximas etapas:

- [Criar um namespace](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)