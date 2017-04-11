<properties 
    pageTitle="Protegendo aplicativos de serviço barramento contra interrupções e desastres | Microsoft Azure"
    description="Descreve técnicas que você pode usar para proteger aplicativos em relação a uma interrupção de serviço barramento possível."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Práticas recomendadas para Protegendo aplicativos contra interrupções de barramento de serviço e desastres

Aplicativos essenciais devem operar continuamente, até mesmo na presença de planejados ou desastres. Este tópico descreve técnicas que você pode usar para proteger aplicativos de serviço barramento contra um desastre ou de interrupção de serviço possíveis.

Uma interrupção é definida como a indisponibilidade temporária de barramento de serviço do Azure. A interrupção pode afetar alguns componentes do barramento de serviço, como um repositório de mensagens ou o Centro de dados inteiro. Depois que o problema foi resolvido, barramento de serviço fica disponível novamente. Normalmente, uma interrupção não causa perda de mensagens ou outros dados. Um exemplo de falha de um componente é indisponibilidade de uma determinada loja de mensagens. Um exemplo de uma interrupção de toda a data center é uma queda de energia de data center ou um switch de rede do data center com defeito. Uma interrupção pode durar de alguns minutos para alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala de barramento de serviço ou data center. Data Center pode ou não pode ser disponibilizado novamente. Normalmente um desastre provoca perda de algumas ou todas as mensagens ou outros dados. Exemplos de desastres são fire, saturação ou terremoto.

## <a name="current-architecture"></a>Arquitetura atual

Barramento de serviço usa vários armazenamentos de mensagens para armazenar as mensagens enviadas para filas ou tópicos. Uma fila não particionados ou o tópico é atribuído a uma loja de mensagens. Se este armazenamento de mensagens não estiver disponível, todas as operações na fila ou tópico falhará.

Todas as entidades de mensagens barramento de serviço (filas, tópicos, retransmissões) residem em um namespace de serviço, que está associado a um data center. Barramento de serviço não habilita a localização geográfica-replicação automática de dados, ele também não permite um namespace abrangência vários data centers.

## <a name="protecting-against-acs-outages"></a>Proteção contra interrupções de ACS

Se você está usando credenciais do ACS e ACS ficar indisponível, clientes já não podem obter tokens. Os clientes que têm um token no momento que ACS desce podem continuar a usar o barramento de serviço até os tokens expirarem. Vida útil do token o padrão é 3 horas.

Para proteger contra interrupções de ACS, use tokens de assinatura de acesso compartilhado (SAS). Nesse caso, o cliente autentica diretamente com barramento de serviço entrando em um token de auto minted com uma chave secreta. Chamadas para ACS não são mais necessárias. Para obter mais informações sobre tokens de SAS, consulte [autenticação de barramento de serviço][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Protegendo tópicos contra falhas de armazenamento de mensagens e filas

Uma fila não particionados ou o tópico é atribuído a uma loja de mensagens. Se este armazenamento de mensagens não estiver disponível, todas as operações na fila ou tópico falhará. Uma fila particionada, por outro lado, consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferentes. Quando uma mensagem é enviada para uma fila particionada ou tópico, barramento de serviço atribui a mensagem para um dos fragmentos. Se o repositório de mensagens correspondente não estiver disponível, barramento de serviço grava a mensagem de um fragmento diferente, se possível. Para obter mais informações sobre entidades particionadas, consulte [particionado entidades mensagens][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Proteção contra interrupções de data center ou desastres

Para permitir um failover entre dois centros de dados, você pode criar um namespace de serviço de barramento de serviço em cada data center. Por exemplo, o barramento de serviço serviço namespace **contosoPrimary.servicebus.windows.net** possam estar localizados na região Norte/Central dos Estados Unidos e **contosoSecondary.servicebus.windows.net** possam estar localizados na região conosco Sul/Central. Se um barramento de serviço entidade de mensagens devem permanecer acessível na presença de uma interrupção do data center, você pode criar entidade em ambos os namespaces.

Para obter mais informações, consulte a seção "Falha do barramento de serviço em um data center Azure" [padrões de mensagens assíncronas e alta disponibilidade][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protegendo pontos de extremidade de retransmissão contra interrupções de data center ou desastres

Localização geográfica replicação de pontos de extremidade de retransmissão permite que um serviço que expõe um ponto de extremidade de retransmissão para ser acessado na presença de interrupções de barramento de serviço. Para obter replicação geográfica, o serviço deve criar dois pontos de extremidade de retransmissão namespaces diferentes. Os namespaces deve residem em diferentes dos data centers e os dois pontos de extremidade devem ter nomes diferentes. Por exemplo, um ponto de extremidade primário possa ser encontrado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto seu correspondente secundário pode ser encontrado em **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço monitora em ambas as extremidades e um cliente pode chamar o serviço por meio das extremidades. Um aplicativo cliente aleatoriamente seleciona um das retransmissões como o principal ponto de extremidade e envia sua solicitação para o ponto de extremidade ativo. Se a operação falhar com um código de erro, essa falha indica que o ponto de extremidade de retransmissão não está disponível. O aplicativo abre um canal para o ponto de extremidade de backup e emite novamente a solicitação. Nesse momento ativo e os pontos de extremidade backup alternam funções: o aplicativo cliente considera o ponto de extremidade ativo antigo para ser o novo ponto de extremidade de backup e o ponto de extremidade de backup antigo seja o novo ponto de extremidade ativo. Se ambos enviar falhas de operações, as funções das duas entidades permanecem inalteradas e um erro será retornado.

A [localização geográfica replicação com mensagens enviadas do serviço barramento][] demonstra como replicar retransmissões.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Protegendo tópicos contra interrupções de data center ou desastres e filas

Para obter resiliência contra interrupções de data center ao usar orientado a mensagens, barramento de serviço compatível com duas abordagens: replicação de *ativos* e *passivos* . Para cada abordagem, se uma determinada fila ou tópico deve permanecer acessível na presença de uma interrupção do data center, você pode criá-lo em ambos os namespaces. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária possa ser encontrada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto seu correspondente secundário pode ser encontrado em **contosoSecondary.servicebus.windows.net/myQueue**.

Se o aplicativo não exige comunicação permanente do remetente ao destinatário, o aplicativo pode implementar uma fila durável do lado do cliente para evitar perda de mensagens e proteger o remetente de qualquer temporárias erros de barramento de serviço.

## <a name="active-replication"></a>Replicação ativa

Replicação ativa usa entidades em ambos os namespaces para cada operação. Qualquer cliente que envia uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada à entidade principal (por exemplo, **contosoPrimary.servicebus.windows.net/sales**), e a segunda cópia da mensagem é enviada para a entidade de secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. O receptor processa a primeira cópia de uma mensagem e a segunda cópia é eliminada. Para suprimir mensagens duplicadas, o remetente deve marcar cada mensagem com um identificador exclusivo. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Você pode usar as propriedades [BrokeredMessage.MessageId][] ou [BrokeredMessage.Label][] ou uma propriedade personalizada para marcar a mensagem. O receptor deve manter uma lista de mensagens que já recebeu.

A [localização geográfica replicação com serviço barramento orientado mensagens][] demonstra replicação ativa da entidades de mensagens.

> [AZURE.NOTE] A abordagem de replicação ativa dobra o número de operações, portanto, essa abordagem pode levar a custo maior.

## <a name="passive-replication"></a>Replicação passiva

No caso livre de falhas, replicação passiva usa apenas uma das duas entidades mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indica a data center que hospeda a entidade ativa pode estar indisponível, o cliente envia uma cópia da mensagem para a entidade de backup. Nesse momento ativo e as entidades backup alternam funções: o cliente de envio considera a entidade de ativa antiga seja a nova entidade de backup e a entidade de backup antiga é o novo ativo. Se ambos enviar falhas de operações, as funções das duas entidades permanecem inalteradas e um erro será retornado.

Um cliente recebe mensagens de ambas as filas. Porque não há uma chance de que o destinatário recebe duas cópias da mesma mensagem, o destinatário deve suprimir mensagens duplicadas. Você pode suprimir duplicatas da mesma maneira, conforme descrito de replicação ativa.

Em geral, replicação passiva é mais econômica do que replicação ativa porque na maioria dos casos, somente uma operação é executada. Latência, produtividade e custo monetário são idênticos ao cenário não replicado.

Ao usar replicação passiva, nas seguintes situações mensagens podem ser perdidas ou recebidas duas vezes:

-   **Atraso de mensagem ou perdas**: suponha que o remetente enviadas com êxito m1 uma mensagem para a fila principal e, em seguida, fila fica indisponível antes do destinatário recebe m1. O remetente envia uma mensagem subsequentes m2 na fila secundário. Se a fila principal está temporariamente indisponível, o receptor recebe m1 após fila fica disponível novamente. No caso de um desastre, o destinatário pode receber nunca m1.

-   **Duplicar recepção**: presumem que o remetente envia uma mensagem m para fila principal. Barramento de serviço com êxito processa m, mas não consegue enviar uma resposta. Após a operação de envio atinge o tempo limite, o remetente envia uma cópia idêntica do m na fila secundário. Se o destinatário é capaz de receber a primeira cópia do m antes fila principal fica indisponível, o destinatário recebe ambas as cópias do m aproximadamente ao mesmo tempo. Se o destinatário não for capaz de receber a primeira cópia do m antes fila principal fica indisponível, o receptor inicialmente recebe somente a segunda cópia do m, mas então recebe uma segunda cópia do m quando a fila principal fica disponível.

A [localização geográfica replicação com serviço barramento orientado mensagens][] demonstra passiva replicação de entidades de mensagens.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a recuperação de dados, consulte estes artigos:

- [Continuidade de negócios de banco de dados do SQL Azure][]
- [Orientações técnicas resiliência Azure][]

  [Autenticação de barramento de serviço]: service-bus-authentication-and-authorization.md
  [Particionado entidades de mensagens]: service-bus-partitioning.md
  [Padrões de mensagens assíncronas e alta disponibilidade]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [Localização geográfica replicação com barramento de serviço enviadas mensagens]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Localização geográfica replicação com barramento de serviço orientado a mensagens]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuidade de negócios de banco de dados do SQL Azure]: ../sql-database/sql-database-business-continuity.md
  [Orientações técnicas resiliência Azure]: ../resiliency/resiliency-technical-guidance.md
