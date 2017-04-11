<properties
   pageTitle="Arquitetura de serviço confiável | Microsoft Azure"
   description="Visão geral da arquitetura do serviço confiável para serviços de estado e sem estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Arquitetura para serviços confiável com informações de estado e sem estado

Um serviço do Azure serviço tecidos confiável pode estar com estado ou sem estado. Cada tipo de serviço é executado dentro de uma arquitetura específico. Essas arquiteturas são descritas neste artigo.
Consulte [Visão geral do serviço confiável](service-fabric-reliable-services-introduction.md) para obter mais informações sobre as diferenças entre serviços com informações de estado e sem estado.

## <a name="stateful-reliable-services"></a>Estado serviços confiáveis

### <a name="architecture-of-a-stateful-service"></a>Arquitetura de um serviço de estado
![Diagrama de arquitetura de um serviço de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Serviço confiável com informações de estado

Um serviço confiável com informações de estado pode derivado da classe a StatefulService ou StatefulServiceBase. Essas duas base classes são fornecidas pelo serviço tecidos. Eles oferecem vários níveis de suporte e abstração para o serviço de estado interajam com o serviço tecidos – e participar como um serviço no cluster tecidos de serviço.

StatefulService deriva StatefulServiceBase. StatefulServiceBase oferece serviços mais flexibilidade, mas exige mais compreensão dos internos do serviço tecidos.
Consulte a [Visão geral de serviço confiável](service-fabric-reliable-services-introduction.md) e o [uso avançado do serviço confiável](service-fabric-reliable-services-advanced-usage.md) para obter mais informações sobre as especificações de escrever serviços usando as classes StatefulService e StatefulServiceBase.

Ambas as classes base gerenciar o ciclo de vida e a função da implementação do serviço. A implementação do serviço pode substituir métodos virtuais de qualquer classe base se a implementação de serviço tem trabalho a fazer naqueles pontos do ciclo de vida de implementação de serviço – ou se quer criar um objeto de ouvinte de comunicação. Observe que, embora uma implementação de serviço pode implementar seu próprio objeto de ouvinte de comunicação expor ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado pelo serviço tecidos – como a implementação de serviço usa um ouvinte de comunicação que é implementado pelo serviço tecidos.

Um serviço confiável com informações de estado usa o Gerenciador de estado confiável para tirar proveito dos conjuntos de confiáveis. Coleções confiáveis são estruturas de dados locais altamente disponíveis para o serviço – o que é, elas são sempre disponíveis, independentemente de failovers de serviço. Cada tipo de coleção confiável é implementado por um provedor de estado confiável.
Para obter mais informações sobre conjuntos de confiáveis, consulte [Visão geral de coleções confiável](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Gerenciador de estado confiável e provedores de estado

O Gerenciador de estado confiável é o objeto que gerencia provedores de estado confiável. Ela tem a funcionalidade para criar, excluir, enumerar e certifique-se de que os provedores de estado confiável são persistentes e altamente disponível. Uma instância do provedor de estado confiável representa uma instância de uma estrutura de dados persistentes e altamente disponível, como um dicionário ou uma fila.

Cada provedor de estado confiável expõe uma interface que é usada por um serviço de estado para interagir com o provedor de estado confiável. Por exemplo, IReliableDictionary é usado para interface com o dicionário confiável, enquanto IReliableQueue é usado para interface com a fila confiável. Todos os provedores de estado confiável implementam a interface IReliableState.

O Gerenciador de estado confiável tem uma interface denominada IReliableStateManager, que permite o acesso a ele de um serviço de estado. Interfaces para provedores de estado confiável são retornadas por meio de IReliableStateManager.

O Gerenciador de estado confiável usa uma arquitetura de plug-in para que novos tipos de conjuntos de confiáveis podem ser conectados dinamicamente.

O dicionário confiável e fila confiável são criados durante a implementação de uma loja diferencial versão de alto desempenho.

### <a name="transactional-replicator"></a>Transações replicator

O componente de transações replicator é responsável por garantir que o estado de um serviço (ou seja, o estado dentro do Gerenciador de estado confiável e as coleções de arquivos confiáveis) é consistente em todas as réplicas executando o serviço. Ele também garante que o estado é mantido no log. As interfaces do Gerenciador de estado confiável com o replicator transações através de um mecanismo particular.

O replicator transações usa um protocolo de rede para comunicar o estado com outras réplicas da instância do serviço para que todas as réplicas tenham informações atualizadas.

O replicator transações usa um log para manter informações de estado para que as informações de estado sobrevive à processo ou nó falha. A interface no log é através de um mecanismo particular.

### <a name="log"></a>Log

O componente de log fornece um armazenamento persistente de alto desempenho que pode ser otimizado para gravação discos girando ou estado sólidos.  O design do log é para o armazenamento persistente (ou seja, discos rígido) ser local para os nós que executam o serviço de estado. Isso permite latências baixos e alta taxa de transferência, em comparação com armazenamento persistente remoto, que não é local para o nó.

O componente de log usa vários arquivos de log. Há um nó toda compartilhado arquivo de log que todas as réplicas usam como ele pode fornecer a latência menor e maior produtividade para armazenar dados de estado. Por padrão, o log de compartilhado é colocado na pasta de trabalho do serviço tecidos nó, mas ele também pode ser configurado para ser colocados em outro local, ideal em um disco reservado para somente o log de compartilhado. Cada réplica do serviço também tem um arquivo de log dedicado e o log de dedicado é colocado dentro do diretório de trabalho do serviço. Não há nenhum mecanismo para configurar o log de dedicada deve ser posicionado em um local diferente.

O log de compartilhado é uma área de transição para informações de estado da réplica, enquanto o arquivo de log dedicado é o destino final onde ele é mantido. Neste design, as informações de estado é primeiro gravadas para o arquivo de log compartilhado e, em seguida, lentamente movidas para o arquivo de log dedicado no plano de fundo. Dessa forma, a gravação no log de compartilhado teria o menor latência e produtividade mais alta que permite que o serviço progredir com mais rapidez.

Leituras e gravações no log compartilhado são feitas por meio de IO direto para pré-distribuído espaço no disco do arquivo de log compartilhado. Para permitir que um uso ideal de espaço em disco na unidade com logs dedicados, o arquivo de log dedicado é criado como um arquivo esparso NTFS. Observe que isso permitirá excesso de provisionamento de espaço em disco e o SO mostrará os arquivos de log dedicado usando muito mais espaço de disco que realmente é usado.

Além de uma interface de usuário-modo mínimo no log, o log é gravado como um driver de modo de núcleo. Executando como um driver de modo de núcleo, o log pode fornecer o melhor desempenho para todos os serviços que usá-lo.

Para obter mais informações sobre como configurar o log, consulte [Configurando estado confiável serviços](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Serviço confiável sem estado

### <a name="architecture-of-a-stateless-service"></a>Arquitetura de um serviço de estado
![Diagrama de arquitetura de um serviço de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Serviço confiável sem estado

Implementações de serviço sem estado derivam da classe StatelessService ou StatelessServiceBase. A classe StatelessServiceBase permite mais flexibilidade do que a classe de StatelessService.
Ambas as classes base gerenciar o ciclo de vida e a função de um serviço.

A implementação do serviço pode substituir métodos virtuais de qualquer classe base se o serviço tem trabalho a fazer naqueles pontos do ciclo de vida do serviço – ou se quer criar um objeto de ouvinte de comunicação. Observe que, embora o serviço pode implementar seu próprio objeto de ouvinte de comunicação expor ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado por estrutura de serviço, como essa implementação de serviço usa um ouvinte de comunicação que é implementado pelo serviço tecidos.

Consulte a [Visão geral de serviço confiável](service-fabric-reliable-services-introduction.md) e o [uso avançado do serviço confiável](service-fabric-reliable-services-advanced-usage.md) para obter mais informações sobre as especificações de escrever serviços usando as classes StatelessService e StatelessServiceBase.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o serviço tecidos, consulte:

[Visão geral do serviço confiável](service-fabric-reliable-services-introduction.md)

[Início rápido](service-fabric-reliable-services-quick-start.md)

[Visão geral das coleções confiável](service-fabric-reliable-services-reliable-collections.md)

[Serviço confiável uso avançado](service-fabric-reliable-services-advanced-usage.md)

[Configuração de serviço confiável](service-fabric-reliable-services-configuration.md)  
