<properties
   pageTitle="Capacidade de teste: Comunicação de serviço | Microsoft Azure"
   description="Serviço de comunicação é um ponto de integração críticos de um aplicativo de serviço tecidos. Este artigo descreve as considerações de design e técnicas de teste."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de capacidade de teste de serviço tecidos: comunicação de serviço

Superfície de estilos de arquitetura orientada a serviços naturalmente na estrutura de serviço do Azure e Microservices. Esses tipos de arquiteturas distribuídas, dividida em componentes microservice aplicativos são geralmente compostos de vários serviços que precisam falar com os outros. Mesmo os casos mais simples, você geralmente têm pelo menos um serviço web sem estado e um serviço de armazenamento de dados com informações de estado que precisam se comunicar.

Serviço de comunicação é um ponto de integração críticos de um aplicativo, porque cada serviço expõe uma API remota para outros serviços. Trabalhar com um conjunto de limites de API que envolve i/o geralmente requer algumas cuidado, com uma boa quantidade de teste e validação.

Há várias considerações a fazer quando esses limites de serviço são ligados juntos em um sistema distribuído:

 - *Protocolo de transporte*. Você usará o HTTP para interoperabilidade de aumento ou um protocolo binário personalizado para transferência máxima?
 - *Manipulação de erro*. Como serão tratados erros permanentes e temporárias? O que acontecerá quando um serviço se move para um nó diferente?
 - *Tempos limite e latência*. Em aplicativos de várias camadas, como cada camada de serviço tratará latência por meio da pilha e para o usuário?

Se você usar um dos componentes de comunicação do serviço interno fornecidos pelo serviço tecidos ou criar seu próprio, teste as interações entre seus serviços é essencial para garantir resiliência em seu aplicativo.

## <a name="prepare-for-services-to-move"></a>Preparar para mover os serviços

Instâncias de serviço podem mover-se ao longo do tempo. Isso é especialmente verdadeiro quando eles são configurados com métricas de carga para balanceamento de recursos ideais personalizada. Serviço tecidos move seu instâncias do serviço para maximizar sua disponibilidade mesmo durante atualizações, failovers, escala-out e outras situações que ocorrem no decorrer de um sistema distribuído.

Como os serviços mover-se no cluster, seus clientes e outros serviços devem estar preparados para lidar com dois cenários quando falam para um serviço:

- A réplica de partição ou instância do serviço foi movida desde a última vez em que você falar a ele. Isso é uma parte de um ciclo de vida do serviço normal e ele deve ser esperava ocorrer durante o ciclo de vida do seu aplicativo.
- A réplica de partição ou instância do serviço está movendo. Embora failover de um serviço de um nó para outro rapidamente ocorre no serviço tecidos, pode haver um atraso na disponibilidade se o componente de comunicação do seu serviço está lento para iniciar.

Manipular esses cenários normalmente é importante para um sistema de execução suave. Para fazer isso, lembre-se de que:

- Cada serviço que pode ser conectado à tem um *endereço* que ele atende (por exemplo, HTTP ou WebSocket). Quando uma instância do serviço ou partição move, altera seu ponto de extremidade do endereço. (Move para um nó diferente com um endereço IP diferente). Se você estiver usando os componentes de comunicação interna, eles tratará novamente resolvendo endereços de serviço para você.
- Pode haver um aumento temporário na latência de serviço como o serviço instância for iniciado seu ouvinte novamente. Isso depende de como o serviço abre rapidamente o ouvinte após a instância do serviço é movida.
- Todas as conexões existentes precisam ser fechado e reaberto depois que o serviço é aberto em um novo nó. Nó normal desligamento ou reinicialização permite tempo para conexões existentes ser desligar normalmente.

### <a name="test-it-move-service-instances"></a>Teste-: mover ocorrências de serviço

Usando ferramentas de capacidade de teste do serviço tecidos, você pode criar um cenário de teste para testar essas situações de maneiras diferentes:

1. Mova réplica principal do serviço com estado.

    A réplica principal de uma partição de serviço com informações de estado pode ser movida para qualquer número de motivos. Use isto para direcionar a réplica primária de uma partição específica para ver como seus serviços reagem a movimentação de forma muito controlada.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Pare de um nó.

    Quando um nó é interrompido, o serviço tecidos move todas as instâncias de serviço ou partições que estavam nesse nó para um dos outros nós disponíveis no cluster. Use isto para testar uma situação onde um nó é perdido em seu cluster e todas as instâncias de serviço e réplicas naquele nó necessário mover.

    Você pode parar de um nó usando o cmdlet do PowerShell **ServiceFabricNode de parada** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade de serviço

Como uma plataforma, estrutura de serviço foi projetada para fornecer alta disponibilidade dos seus serviços. Mas em casos extremos, problemas de infraestrutura subjacentes ainda podem causar indisponibilidade. É importante testar para esses cenários, também.

Serviços de estado usam um sistema baseado em quorum para replicar estado para alta disponibilidade. Isso significa que um quorum de réplicas precisa estar disponível para executar operações de gravação. Em casos raros, como uma falha de hardware amplo, um quorum de réplicas pode não estar disponível. Nesses casos, não será capaz de executar operações de gravação, mas ainda será capaz de executar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Teste-: escrever indisponibilidade de operação

Usando as ferramentas de capacidade de teste em estrutura de serviço, você pode inserir uma falha que induz perda de quorum como um teste. Embora esse cenário é raro, é importante que os clientes e os serviços que dependem de um serviço de estado estão preparados para lidar com situações em que eles não podem fazer solicitações de gravação para ele. Também é importante que o próprio serviço estado é ciente dessa possibilidade e pode normalmente se comunicá-lo aos chamadores.

Você pode induzir perda de quorum usando o cmdlet do PowerShell **Chamar ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` para `QuorumReplicas` para indicar que queremos de induzi perda de quorum sem interromper todas as réplicas. Dessa forma, as operações de leitura ainda são possíveis. Para testar um cenário onde toda uma partição não estiver disponível, você pode definir essa opção para `AllReplicas`.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre as ações de capacidade de teste](service-fabric-testability-actions.md)

[Saiba mais sobre cenários de capacidade de teste](service-fabric-testability-scenarios.md)
