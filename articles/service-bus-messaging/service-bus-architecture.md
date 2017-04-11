<properties 
    pageTitle="Arquitetura de barramento de serviço | Microsoft Azure"
    description="Descreve a mensagem e retransmissão processamento arquitetura do barramento de serviço do Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Arquitetura de barramento de serviço

Este artigo descreve a mensagem e retransmissão processamento arquitetura do barramento de serviço do Azure.

## <a name="service-bus-scale-units"></a>Unidades de escala de barramento de serviço

Barramento de serviço é organizado por *unidades de escala*. Uma unidade de escala é uma unidade de implantação e contém todos os componentes necessários executar o serviço. Cada região implanta uma ou mais unidades de escala de barramento de serviço.

Um namespace barramento de serviço é mapeado para uma unidade de escala. A unidade de escala trata todos os tipos de entidades de barramento de serviço: retransmissões e comunicação entidades mensagens (filas, tópicos, assinaturas). Uma unidade de escala de barramento de serviço consiste nos seguintes componentes:

- **Um conjunto de nós do gateway.** Nós de gateway autenticam solicitações de entrada e lidar com solicitações de retransmissão. Cada nó de gateway tem um endereço IP público.

- **Um conjunto de nós do agente de mensagens.** Nós de agente mensagens processam solicitações referentes à entidades mensagens.

- **Repositório de um gateway.** O armazenamento de gateway mantém os dados para cada entidade que é definida desta unidade de escala. O armazenamento de gateway é implementado na parte superior de um banco de dados do SQL Azure.

- **Vários armazenamentos de mensagens.** Armazena mensagens mantenha as mensagens de todas as filas, tópicos e assinaturas definidos nesta unidade de escala. Ele também contém todos os dados de assinatura. A menos que [particionada entidades de mensagens](service-bus-partitioning.md) está habilitada, uma fila ou tópico é mapeado para um armazenamento de mensagens. Assinaturas são armazenadas no mesmo repositório mensagens como seu tópico pai. Exceto para barramento de serviço [Premium de mensagens](service-bus-premium-messaging.md), o armazena mensagens é implementado sobre bancos de dados do SQL Azure.

## <a name="containers"></a>Contêineres

Cada entidade de mensagens é atribuída um contêiner específico. Um contêiner é uma construção lógica que usa exatamente uma loja para loja de mensagens todos os dados relevantes para este contêiner. Cada contêiner é atribuído a um nó agente mensagens. Normalmente, há mais contêineres de nós do agente de mensagens. Portanto, cada nó agente mensagens carrega vários contêineres. A distribuição dos contêineres de um nó agente mensagens é organizada de forma que todos os nós de agente mensagens igualmente são carregados. Se a carga padrão alterações (por exemplo, uma das obtém contêineres muito ocupado), ou se um nó agente mensagens fica temporariamente indisponível, os contêineres são redistribuídos entre os nós agente mensagens.

## <a name="processing-of-incoming-messaging-requests"></a>Processamento de solicitações de entrada de mensagens

Quando um cliente envia uma solicitação para barramento de serviço, o balanceador de carga Azure roteia-o para qualquer um de nós do gateway. O nó de gateway autoriza a solicitação. Se a solicitação preocupações uma entidade de mensagens (fila, tópico, assinatura), o nó de gateway procura a entidade no repositório do gateway e determina quais mensagens loja a entidade está localizada. Ela procura então qual nó agente mensagens atualmente está servindo este contêiner e envia a solicitação ao nó agente mensagens. O nó agente mensagens processa a solicitação e atualiza o estado de entidade no repositório do contêiner. O nó agente mensagens envia a resposta de volta para o nó de gateway, que envia uma resposta apropriada para o cliente que emitiu a solicitação original.

![Processamento de solicitações de entrada de mensagens](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Processamento de retransmissão de solicitações de entrada

Quando um cliente envia uma solicitação para barramento de serviço, o balanceador de carga Azure roteia-o para qualquer um de nós do gateway. Se a solicitação for uma solicitação de ouvinte, o nó de gateway cria uma retransmissão de novo. Se a solicitação for uma solicitação de conexão para uma retransmissão específica, o nó de gateway encaminha a solicitação de conexão para o nó de gateway que possui a retransmissão. O nó de gateway que possui o relay envia uma solicitação de reunião para o cliente ouvinte, solicitando que o ouvinte para criar um canal temporário para o nó de gateway que recebeu a solicitação de conexão.

Quando a conexão de retransmissão é estabelecida, os clientes podem trocar mensagens via o nó de gateway que é usado para a reunião.

![Processamento de retransmissão de solicitações de entrada](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você já leu uma visão geral de arquitetura de barramento de serviço, começar a visite os links a seguir:

- [Visão geral de mensagens do barramento de serviço](service-bus-messaging-overview.md)
- [Fundamentos de barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Uma solução de mensagens na fila usando filas de barramento de serviço](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
