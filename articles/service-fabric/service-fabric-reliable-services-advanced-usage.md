<properties
   pageTitle="Uso dos serviços confiável avançado | Microsoft Azure"
   description="Saiba mais sobre o uso avançado de serviços de confiável do serviço tecidos para mais flexibilidade em seus serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Uso dos serviços confiável modelo de programação avançado
Estrutura de serviço Azure simplifica escrita e gerenciamento de serviços de com e sem monitoração confiáveis. Este guia falar sobre usos avançados de serviços confiáveis para obter mais flexibilidade e controle sobre seus serviços. Antes de ler este guia, familiarize-se com [O modelo de programação de serviços confiável](service-fabric-reliable-services-introduction.md).

Estado e sem estado serviços têm dois pontos de entrada principal para código do usuário:

 - `RunAsync`é um ponto de entrada de uso geral para seu código de serviço.
 - `CreateServiceReplicaListeners`e `CreateServiceInstanceListeners` é para abrir ouvintes de comunicação para solicitações de cliente.
 
Para a maioria dos serviços, esses pontos de dois entrada são suficientes. Em casos raros quando mais controle sobre o ciclo de vida de um serviço é necessária, eventos de ciclo de vida adicionais estão disponíveis.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida de instância de serviço sem estado

Ciclo de vida de um estado serviço é muito simple. Um serviço de estado só pode ser aberto, fechado ou anulado. `RunAsync`em um serviço de estado é executado quando uma instância do serviço é aberta e cancelada quando uma instância do serviço é fechada ou cancelada. 

Embora `RunAsync` devem ser suficientes em quase todos os casos, abrir, fechar e anular eventos em um serviço de estado também estão disponíveis:

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync é chamado quando a instância do serviço de estado estiver prestes a ser usado. Tarefas de inicialização do serviço estendido podem ser iniciadas no momento.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync é chamado quando a instância do serviço sem estado vai ser desligar. Isso pode ocorrer quando o código do serviço está sendo atualizado, a instância do serviço está sendo movida devido a balanceamento de carga ou uma falha temporária for detectada. OnCloseAsync pode ser usado para fechar todos os recursos, parar qualquer processamento de plano de fundo, terminar de salvar estado externo ou fechar conexões existentes com segurança.

- `void OnAbort()`
  OnAbort é chamado quando a instância do serviço de estado está sendo desligada forçada. Isso geralmente é chamado quando é detectada uma falha permanente no nó ou serviço tecidos confiavelmente não pode gerenciar o ciclo de vida da instância do serviço devido a falhas internos.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida do serviço estado réplica

Ciclo de vida do serviço estado réplica é muito mais complexo do que uma instância de serviço sem estado. Além para abrir, fechar e anular eventos, uma réplica de serviço estado passa por alterações de função durante sua vida útil. Quando uma réplica de serviço estado altera a função, o `OnChangeRoleAsync` evento for disparado:

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync é chamado quando a réplica de estado de serviço está mudando a função, por exemplo para primário ou secundário. Réplicas primárias recebem status de gravação (são permitidos para criar e gravar conjuntos confiável). Réplicas secundárias recebem status de leitura (somente pode ler de conjuntos de confiável existente). A maioria dos trabalho em um serviço de estado é executado na réplica principal. Réplicas secundárias podem realizar validação de somente leitura, geração de relatórios, mineração de dados ou outros trabalhos de somente leitura.

Em um serviço de estado, somente a réplica principal tem acesso de gravação ao estado e, portanto, é geralmente quando o serviço está executando o trabalho real. O `RunAsync` método em um serviço de estado é executado somente quando a réplica de serviço com informações de estado é primária. O `RunAsync` método seja cancelado quando função da réplica principal é alterada longe principal, bem como durante os eventos de fechamento e anular. 

Usando o `OnChangeRoleAsync` evento permite que você realizar o trabalho dependendo das funções de réplica, bem como em resposta a alteração de função.

Um serviço de estado também fornece os mesmos eventos de ciclo de vida quatro como um serviço independente, com a mesma semântica e casos de uso:

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Próximas etapas
Para mais avançados tópicos relacionados ao serviço tecidos, consulte os seguintes artigos:

- [Configurar serviços confiáveis com informações de estado](service-fabric-reliable-services-configuration.md)

- [Introdução de integridade do serviço tecidos](service-fabric-health-introduction.md)

- [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configurando serviços com o Gerenciador de recursos de Cluster tecidos do serviço](service-fabric-cluster-resource-manager-configure-services.md)
