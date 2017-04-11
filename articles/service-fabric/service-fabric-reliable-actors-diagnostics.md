<properties
   pageTitle="Atores diagnóstico e monitoramento | Microsoft Azure"
   description="Este artigo descreve o diagnóstico e recursos no runtime atores confiável do serviço tecidos, incluindo os eventos e contadores de desempenho emitidos por ele de monitoramento de desempenho."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitoramento do desempenho para atores confiável
O tempo de execução de atores confiável emite [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Esses fornecem visões como o tempo de execução está funcionando e ajudar na solução de problemas e monitoramento do desempenho.

## <a name="eventsource-events"></a>EventSource eventos
O nome do provedor de EventSource para o runtime atores confiável é "Microsoft-ServiceFabric-atores". Eventos de origem este evento aparecem na janela de [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o aplicativo de ator está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajude a coletar e/ou exibindo EventSource eventos são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Log semântico](https://msdn.microsoft.com/library/dn774980.aspx)e a [Biblioteca de TraceEvent da Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem a EventSource atores confiável estão associados com uma ou mais palavras-chave. Isso permite a filtragem de eventos que são coletados. Os bits de palavra-chave a seguir são definidos.

|Bit|Descrição|
|---|---|
|0x1|Conjunto de eventos importantes que resumem a operação do runtime tecidos atores.|
|0x2|Conjunto de eventos que descrevem chamadas de método de ator. Para obter mais informações, consulte o [tópico introdutório em atores](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Conjunto de eventos relacionados ao estado do ator. Para obter mais informações, consulte o tópico sobre [gerenciamento de estado de ator](service-fabric-reliable-actors-state-management.md).|
|0x8|Conjunto de eventos relacionados à concorrência baseado em vez do ator. Para obter mais informações, consulte o tópico sobre [concorrência](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução de atores confiável define as seguintes categorias de contador de desempenho.

|Categoria|Descrição|
|---|---|
|Serviço tecidos ator|Contadores específicos de atores tecidos de serviço do Azure, por exemplo, temporais necessário para salvar o estado de ator|
|Método de ator de tecidos do serviço|Contadores específicos para métodos implementados por atores tecidos do serviço, por exemplo, com que frequência um ator método é invocado|

Cada uma das categorias acima tem um ou mais contadores.

O aplicativo de [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operacional Windows pode ser usado para coletar e exibir dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados de contador de desempenho e carregá-la tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instância de contador de desempenho
Um cluster que tem um grande número de serviços de ator ou partições de serviço de ator terá um grande número de instâncias de contador de desempenho do ator. Os nomes de ocorrência do contador de desempenho podem ajudar a identificar o método de [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e ator específico (se aplicável) que a instância de contador de desempenho está associada.

#### <a name="service-fabric-actor-category"></a>Categoria de serviço tecidos ator
Para a categoria `Service Fabric Actor`, os nomes de ocorrência do contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da identificação da partição tecidos de serviço que a instância de contador de desempenho está associada. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada por meio do [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo runtime tecidos atores para seu uso interno. Isso é incluído no nome da instância de contador de desempenho, para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

A seguir é um exemplo de um nome de instância do contador para um contador que pertence a `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de caracteres de identificação da partição tecidos de serviço e `635650083799324046` é usar a identificação de 64 bits que é gerada para o tempo de execução interno.

#### <a name="service-fabric-actor-method-category"></a>Categoria de serviço tecidos ator método
Para a categoria `Service Fabric Actor Method`, os nomes de ocorrência do contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método ator que a instância de contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no runtime tecidos atores que equilibra a legibilidade do nome com restrições sobre o comprimento máximo dos nomes de ocorrência do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de cadeia de caracteres de um inteiro de 32 bits que é gerado pelo runtime tecidos atores para seu uso interno. Isso é incluído no nome da instância de contador de desempenho, para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da identificação da partição tecidos de serviço que a instância de contador de desempenho está associada. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada por meio do [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo runtime tecidos atores para seu uso interno. Isso é incluído no nome da instância de contador de desempenho, para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome de instância do contador de desempenho.

A seguir é um exemplo de um nome de instância do contador para um contador que pertence a `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, `2` é a ID de 32 bits gerada para uso interno do runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de caracteres de identificação da partição tecidos de serviço e `635650083804480486` é usar a identificação de 64 bits gerada para o tempo de execução interno.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho

### <a name="actor-method-events-and-performance-counters"></a>Contadores de desempenho e eventos de método de ator
O tempo de execução de atores confiável emite os seguintes eventos relacionados a [métodos de ator](service-fabric-reliable-actors-introduction.md#actors).

|Nome do evento|Identificação do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorMethodStart|7|Detalhado|0x2|Tempo de execução de atores está prestes a chamar um método de ator.|
|ActorMethodStop|8|Detalhado|0x2|Um método de ator terminar a execução. Isto é, a chamada assíncrona do runtime para o método de ator retornou e a tarefa retornada pelo método ator terminou.|
|ActorMethodThrewException|9|Aviso|0x3|Ocorreu uma exceção durante a execução de um método de ator, durante a chamada assíncrona do runtime para o método de ator ou durante a execução da tarefa retornado pelo método ator. Esse evento indica algum tipo de falha no código de ator que precisa investigação.|

O tempo de execução de atores confiável publica os seguintes contadores de desempenho relacionados à execução dos métodos de ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Método de ator de tecidos do serviço|Chamadas/seg|Número de vezes que o método de serviço do ator é invocado por segundo|
|Método de ator de tecidos do serviço|Médio milissegundos por invocação|Tempo necessário para executar o método de serviço do ator em milissegundos|
|Método de ator de tecidos do serviço|Exceções descartadas/s|Número de vezes que o ator serviço método emitiu uma exceção por segundo|

### <a name="concurrency-events-and-performance-counters"></a>Contadores de desempenho e eventos de concorrência
O tempo de execução de atores confiável emite os seguintes eventos relacionados à [concorrência](service-fabric-reliable-actors-introduction.md#concurrency).

|Nome do evento|Identificação do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Detalhado|0x8|Este evento é escrito no início de cada novo entregar quadro de um ator. Ele contém o número de pendentes chamadas de ator que estão aguardando para adquirir o bloqueio por ator que impõe concorrência baseado em Ativar.|

O tempo de execução de atores confiável publica os seguintes contadores de desempenho relacionados à concorrência.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço tecidos ator|# de chamadas de ator aguardando bloqueio de ator|Número de pendentes chamadas de ator aguardando para adquirir o bloqueio por ator que impõe baseado em Ativar concorrência|
|Serviço tecidos ator|Aguardar milissegundos médio por bloquear|Tempo decorrido (em milissegundos) para adquirir o bloqueio por ator que impõe baseado em Ativar concorrência|
|Serviço tecidos ator|Média de milissegundos ator bloqueio mantido|Tempo (em milissegundos) para o qual o bloqueio por ator é mantido|

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gerenciamento de estado de ator e contadores de desempenho
O tempo de execução de atores confiável emite os seguintes eventos relacionados a [gerenciamento de estado de ator](service-fabric-reliable-actors-state-management.md).

|Nome do evento|Identificação do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorSaveStateStart|10|Detalhado|0x4|Tempo de execução de atores está prestes a salvar o estado de ator.|
|ActorSaveStateStop|11|Detalhado|0x4|Tempo de execução de atores terminou de salvar o estado de ator.|

O tempo de execução de atores confiável publica os seguintes contadores de desempenho relacionados a gerenciamento de estado de ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço tecidos ator|Operação de estado de salvamento de milissegundos médio por|Tempo necessário para salvar o estado de ator em milissegundos|
|Serviço tecidos ator|Médio milissegundos por operação de estado de carregamento|Tempo necessário para carregar o estado de ator em milissegundos|

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados ao réplicas ator
O tempo de execução de atores confiável emite os seguintes eventos relacionados a [réplicas ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nome do evento|Identificação do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativo|0x1|Réplica do ator alterado função principal. Isso significa que os atores para esta partição serão criados dentro desta réplica.|
|ReplicaChangeRoleFromPrimary|2|Informativo|0x1|Réplica do ator alterado função para não principal. Isso significa que os atores para esta partição já não serão criados dentro desta réplica. Não há novas solicitações sejam entregues em atores já criados dentro desta réplica. Os atores serão destruídos após todas as solicitações em andamento.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Contadores de desempenho e eventos de ativação e desativação do ator
O tempo de execução de atores confiável emite os seguintes eventos relacionados a [desativação e ativação de ator](service-fabric-reliable-actors-lifecycle.md).

|Nome do evento|Identificação do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorActivated|5|Informativo|0x1|Um ator foi ativado.|
|ActorDeactivated|6|Informativo|0x1|Um ator foi desativado.|

O tempo de execução de atores confiável publica os seguintes contadores de desempenho relacionados a desativação e ativação de ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço tecidos ator|Média OnActivateAsync milissegundos|Tempo necessário para executar o método OnActivateAsync em milissegundos|

### <a name="actor-request-processing-performance-counters"></a>Contadores de desempenho de processamento de solicitação de ator
Quando um cliente invoca um método por meio de um objeto de proxy do ator, ele resulta em uma mensagem de solicitação está sendo enviada pela rede para o serviço de ator. O serviço processa a mensagem de solicitação e envia uma resposta de volta para o cliente. O tempo de execução de atores confiável publica os seguintes contadores de desempenho relacionados ao processamento de solicitação de ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço tecidos ator|# de solicitações pendentes|Número de solicitações sendo processadas no serviço|
|Serviço tecidos ator|Médio milissegundos por solicitação|Tempo decorrido (em milissegundos) pelo serviço para processar uma solicitação|
|Serviço tecidos ator|Médio milissegundos para desserialização de solicitação|Tempo decorrido (em milissegundos) para desserializar a mensagem de solicitação de ator quando for recebida no serviço|
|Serviço tecidos ator|Médio milissegundos para serialização de resposta|Tempo decorrido (em milissegundos) para serializar a mensagem de resposta do ator no serviço antes da resposta é enviada para o cliente|

## <a name="next-steps"></a>Próximas etapas
 - [Como confiável atores usar a plataforma de estrutura de serviço](service-fabric-reliable-actors-platform.md)
 - [Documentação de referência de ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)
