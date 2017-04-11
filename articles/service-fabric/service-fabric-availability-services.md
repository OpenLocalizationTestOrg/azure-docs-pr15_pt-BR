<properties
   pageTitle="Disponibilidade de serviços de serviço tecidos | Microsoft Azure"
   description="Descreve a detecção de falhas, failover e recuperação para serviços"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="availability-of-service-fabric-services"></a>Disponibilidade de serviços de estrutura de serviço
Serviços de serviço tecidos Azure podem ser com estado ou sem estado. Este artigo fornece uma visão geral de como serviço tecidos mantém a disponibilidade de um serviço em caso de falhas.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade de serviços de estado de estrutura de serviço
Um serviço de estado é um serviço de aplicativo que não tem nenhum [estado persistente local](service-fabric-concepts-state.md).

Criando um serviço sem estado requer definindo uma contagem de instância, o que é o número de instâncias do serviço sem estado que deve ser executado no cluster. Este é o número de cópias da lógica do aplicativo que será instanciado no cluster. Aumentar o número de instâncias é a maneira recomendada de dimensionamento de um serviço de estado.

Quando uma falha for detectada em qualquer instância de um serviço independente, uma nova instância é criada em outro nó qualificado no cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade de serviços de estado de estrutura de serviço
Um serviço com estado tem algum estado associado a ela. Em estrutura de serviço, um serviço de estado é estabelecido como um conjunto de réplicas. Cada réplica é uma instância do código do serviço que tem uma cópia do estado. Operações de leitura e gravação são realizadas em uma réplica (chamada principal). Alterações em estado de gravação operações são *replicados* para várias outras réplicas (chamado secundários ativos). A combinação de réplicas secundárias primárias e ativas é o conjunto de réplica do serviço.

Pode haver apenas um ler e gravar solicitações manutenção de réplica principal, mas pode haver várias cópias secundárias ativas. O número de cópias secundárias ativas é configurável e um número maior de réplicas pode suportar um número maior de falhas de hardware e software simultânea.

No evento de uma falha (quando a réplica principal falhar), o serviço tecidos faz um das cópias secundárias ativas a nova réplica primária. Esta réplica secundária ativa já tem a versão atualizada do estado (via *replicação*), e ele pode continuar a leitura adicional de processamento e operações de gravação.

Esse conceito – de uma réplica sendo um principal ou ativo secundário – é conhecido como a função de réplica.

### <a name="replica-roles"></a>Funções de réplica
A função de uma réplica é usada para gerenciar o ciclo de vida do estado sendo gerenciado pelo réplica. Solicitações de leitura de uma réplica cuja função seja serviços principal. Ele também serviços solicitações de gravação atualizando seu estado e replica as alterações para secundários ativos em seu conjunto de réplica. A função de um secundário ativo é receber alterações de estado que a réplica principal tem replicados e atualizar seu modo de exibição do estado.

>[AZURE.NOTE] Modelos de programação de alto nível como o [framework atores confiável](service-fabric-reliable-actors-introduction.md) ausente resumem o conceito de função de réplica do desenvolvedor.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os conceitos de estrutura de serviço, consulte o seguinte:

- [Escalabilidade dos serviços de estrutura de serviço](service-fabric-concepts-scalability.md)

- [Serviços de serviço tecidos partição](service-fabric-concepts-partitioning.md)

- [Definir e gerenciar estado](service-fabric-concepts-state.md)
