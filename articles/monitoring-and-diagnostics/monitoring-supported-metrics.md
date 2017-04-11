<properties
    pageTitle="Métricas de Monitor Azure - métricas suportados por tipo de recurso | Microsoft Azure"
    description="Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Monitor do Azure

Monitor Azure oferece várias maneiras para interagir com métricas, incluindo gráficos-los no portal do, acessá-los por meio da API REST ou consultando-los usando o PowerShell ou CLI. Abaixo está uma lista completa de todas as métricas disponível atualmente com pipeline métrica do Monitor do Azure.

> [AZURE.NOTE] Outras métricas podem estar disponíveis no portal do ou usando APIs herdadas. Essa lista inclui apenas métricas de demonstração pública disponíveis usando a visualização pública do pipeline métrica Azure Monitor consolidado.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CoreCount|Contagem de núcleo|Contagem|Total|Número total de cores na conta em lotes|
|TotalNodeCount|Contagem de nós|Contagem|Total|Número total de nós na conta em lotes|
|CreatingNodeCount|Criação de contagem de nós|Contagem|Total|Número de nós sendo criado|
|StartingNodeCount|Contagem de nó inicial|Contagem|Total|Número de nós iniciando|
|WaitingForStartTaskNodeCount|Aguardando para contagem de nós de tarefa do início|Contagem|Total|Número de nós aguardando a começar a conclusão da tarefa|
|StartTaskFailedNodeCount|Contagem de nós falha da tarefa de início|Contagem|Total|Número de nós onde a tarefa iniciar falhou|
|IdleNodeCount|Contagem de nós ocioso|Contagem|Total|Número de nós ociosos|
|OfflineNodeCount|Contagem de nós offline|Contagem|Total|Número de nós off-line|
|RebootingNodeCount|Reiniciar contagem de nós|Contagem|Total|Número de reinicializar nós|
|ReimagingNodeCount|Contagem de nós de fazer uma nova imagem|Contagem|Total|Número de nós de fazer uma nova imagem|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número da execução nós|
|LeavingPoolNodeCount|Deixando a contagem de nós do Pool|Contagem|Total|Número de nós deixando o Pool|
|UnusableNodeCount|Contagem de nós inutilizável|Contagem|Total|Número de nós inutilizáveis|
|TaskStartEvent|Eventos de início de tarefas|Contagem|Total|Número total de tarefas que foram iniciados|
|TaskCompleteEvent|Eventos de tarefa concluída|Contagem|Total|Número total de tarefas que foram concluídas|
|TaskFailEvent|Eventos de falhas de tarefas|Contagem|Total|Número total de tarefas que foram concluídas em um estado de falha|
|PoolCreateEvent|Pool criar eventos|Contagem|Total|Número total de pools que foram criadas|
|PoolResizeStartEvent|Eventos de início de redimensionamento do pool|Contagem|Total|Número total de redimensiona pool que tenha iniciado|
|PoolResizeCompleteEvent|Eventos de completa de redimensionamento do pool|Contagem|Total|Número total de redimensiona pool que foram concluídas|
|PoolDeleteStartEvent|Eventos de início do pool Delete|Contagem|Total|Número total de exclusões de pool que foram iniciados|
|PoolDeleteCompleteEvent|Eventos completa do pool Delete|Contagem|Total|Número total de exclusões de pool que concluiu|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||
|totalcommandsprocessed|Total de operações|Contagem|Total||
|cachehits|Ocorrências de cache|Contagem|Total||
|cachemisses|Erros de cache|Contagem|Total||
|GetCommands|Obtém|Contagem|Total||
|setcommands|Conjuntos|Contagem|Total||
|evictedkeys|Chaves removidas|Contagem|Total||
|totalkeys|Total de chaves|Contagem|Máximo||
|expiredkeys|Chaves expiradas|Contagem|Total||
|usedmemory|Memória usada|Bytes|Máximo||
|usedmemoryRss|Memória usada RSS|Bytes|Máximo||
|serverLoad|Carga do servidor|Porcentagem|Máximo||
|cacheWrite|Gravação de cache|BytesPerSecond|Máximo||
|cacheRead|Leitura de cache|BytesPerSecond|Máximo||
|percentProcessorTime|CPU|Porcentagem|Máximo||
|connectedclients0|Clientes conectados (fragmentar 0)|Contagem|Máximo||
|totalcommandsprocessed0|Total de operações (fragmentar 0)|Contagem|Total||
|cachehits0|Ocorrências de cache (fragmentar 0)|Contagem|Total||
|cachemisses0|Erros de cache (fragmentar 0)|Contagem|Total||
|getcommands0|Obtém (fragmentar 0)|Contagem|Total||
|setcommands0|Conjuntos (fragmentar 0)|Contagem|Total||
|evictedkeys0|Chaves removidas (fragmentar 0)|Contagem|Total||
|totalkeys0|Total de chaves (nó 0)|Contagem|Máximo||
|expiredkeys0|Chaves expiradas (fragmentar 0)|Contagem|Total||
|usedmemory0|Memória usada (fragmentar 0)|Bytes|Máximo||
|usedmemoryRss0|Memória usada RSS (fragmentar 0)|Bytes|Máximo||
|serverLoad0|Carga do servidor (fragmentar 0)|Porcentagem|Máximo||
|cacheWrite0|Gravação de cache (fragmentar 0)|BytesPerSecond|Máximo||
|cacheRead0|Leitura de cache (fragmentar 0)|BytesPerSecond|Máximo||
|percentProcessorTime0|CPU (fragmentar 0)|Porcentagem|Máximo||
|connectedclients1|Clientes conectados (fragmentar 1)|Contagem|Máximo||
|totalcommandsprocessed1|Total de operações (fragmentar 1)|Contagem|Total||
|cachehits1|Ocorrências de cache (fragmentar 1)|Contagem|Total||
|cachemisses1|Erros de cache (fragmentar 1)|Contagem|Total||
|getcommands1|Obtém (fragmentar 1)|Contagem|Total||
|setcommands1|Conjuntos (fragmentar 1)|Contagem|Total||
|evictedkeys1|Chaves removidas (fragmentar 1)|Contagem|Total||
|totalkeys1|Total de chaves (nó 1)|Contagem|Máximo||
|expiredkeys1|Chaves expiradas (fragmentar 1)|Contagem|Total||
|usedmemory1|Memória usada (fragmentar 1)|Bytes|Máximo||
|usedmemoryRss1|Memória usada RSS (fragmentar 1)|Bytes|Máximo||
|serverLoad1|Carga do servidor (fragmentar 1)|Porcentagem|Máximo||
|cacheWrite1|Gravação de cache (fragmentar 1)|BytesPerSecond|Máximo||
|cacheRead1|Leitura de cache (fragmentar 1)|BytesPerSecond|Máximo||
|percentProcessorTime1|CPU (fragmentar 1)|Porcentagem|Máximo||
|connectedclients2|Clientes conectados (fragmentar 2)|Contagem|Máximo||
|totalcommandsprocessed2|Total de operações (fragmentar 2)|Contagem|Total||
|cachehits2|Ocorrências de cache (fragmentar 2)|Contagem|Total||
|cachemisses2|Erros de cache (fragmentar 2)|Contagem|Total||
|getcommands2|Obtém (fragmentar 2)|Contagem|Total||
|setcommands2|Conjuntos (fragmentar 2)|Contagem|Total||
|evictedkeys2|Chaves removidas (fragmentar 2)|Contagem|Total||
|totalkeys2|Total de chaves (nó 2)|Contagem|Máximo||
|expiredkeys2|Chaves expiradas (fragmentar 2)|Contagem|Total||
|usedmemory2|Memória usada (fragmentar 2)|Bytes|Máximo||
|usedmemoryRss2|Memória usada RSS (fragmentar 2)|Bytes|Máximo||
|serverLoad2|Carga do servidor (fragmentar 2)|Porcentagem|Máximo||
|cacheWrite2|Gravação de cache (fragmentar 2)|BytesPerSecond|Máximo||
|cacheRead2|Leitura de cache (fragmentar 2)|BytesPerSecond|Máximo||
|percentProcessorTime2|CPU (fragmentar 2)|Porcentagem|Máximo||
|connectedclients3|Clientes conectados (fragmentar 3)|Contagem|Máximo||
|totalcommandsprocessed3|Total de operações (fragmentar 3)|Contagem|Total||
|cachehits3|Ocorrências de cache (fragmentar 3)|Contagem|Total||
|cachemisses3|Erros de cache (fragmentar 3)|Contagem|Total||
|getcommands3|Obtém (fragmentar 3)|Contagem|Total||
|setcommands3|Conjuntos (fragmentar 3)|Contagem|Total||
|evictedkeys3|Chaves removidas (fragmentar 3)|Contagem|Total||
|totalkeys3|Total de chaves (nó 3)|Contagem|Máximo||
|expiredkeys3|Chaves expiradas (fragmentar 3)|Contagem|Total||
|usedmemory3|Memória usada (fragmentar 3)|Bytes|Máximo||
|usedmemoryRss3|Memória usada RSS (fragmentar 3)|Bytes|Máximo||
|serverLoad3|Carga do servidor (fragmentar 3)|Porcentagem|Máximo||
|cacheWrite3|Gravação de cache (fragmentar 3)|BytesPerSecond|Máximo||
|cacheRead3|Leitura de cache (fragmentar 3)|BytesPerSecond|Máximo||
|percentProcessorTime3|CPU (fragmentar 3)|Porcentagem|Máximo||
|connectedclients4|Clientes conectados (fragmentar 4)|Contagem|Máximo||
|totalcommandsprocessed4|Total de operações (fragmentar 4)|Contagem|Total||
|cachehits4|Ocorrências de cache (fragmentar 4)|Contagem|Total||
|cachemisses4|Erros de cache (fragmentar 4)|Contagem|Total||
|getcommands4|Obtém (fragmentar 4)|Contagem|Total||
|setcommands4|Conjuntos (fragmentar 4)|Contagem|Total||
|evictedkeys4|Chaves removidas (fragmentar 4)|Contagem|Total||
|totalkeys4|Total de chaves (nó 4)|Contagem|Máximo||
|expiredkeys4|Chaves expiradas (fragmentar 4)|Contagem|Total||
|usedmemory4|Memória usada (fragmentar 4)|Bytes|Máximo||
|usedmemoryRss4|Memória usada RSS (fragmentar 4)|Bytes|Máximo||
|serverLoad4|Carga do servidor (fragmentar 4)|Porcentagem|Máximo||
|cacheWrite4|Gravação de cache (fragmentar 4)|BytesPerSecond|Máximo||
|cacheRead4|Leitura de cache (fragmentar 4)|BytesPerSecond|Máximo||
|percentProcessorTime4|CPU (fragmentar 4)|Porcentagem|Máximo||
|connectedclients5|Clientes conectados (fragmentar 5)|Contagem|Máximo||
|totalcommandsprocessed5|Total de operações (fragmentar 5)|Contagem|Total||
|cachehits5|Ocorrências de cache (fragmentar 5)|Contagem|Total||
|cachemisses5|Erros de cache (fragmentar 5)|Contagem|Total||
|getcommands5|Obtém (fragmentar 5)|Contagem|Total||
|setcommands5|Conjuntos (fragmentar 5)|Contagem|Total||
|evictedkeys5|Chaves removidas (fragmentar 5)|Contagem|Total||
|totalkeys5|Total de chaves (nó 5)|Contagem|Máximo||
|expiredkeys5|Chaves expiradas (fragmentar 5)|Contagem|Total||
|usedmemory5|Memória usada (fragmentar 5)|Bytes|Máximo||
|usedmemoryRss5|Memória usada RSS (fragmentar 5)|Bytes|Máximo||
|serverLoad5|Carga do servidor (fragmentar 5)|Porcentagem|Máximo||
|cacheWrite5|Gravação de cache (fragmentar 5)|BytesPerSecond|Máximo||
|cacheRead5|Leitura de cache (fragmentar 5)|BytesPerSecond|Máximo||
|percentProcessorTime5|CPU (fragmentar 5)|Porcentagem|Máximo||
|connectedclients6|Clientes conectados (fragmentar 6)|Contagem|Máximo||
|totalcommandsprocessed6|Total de operações (fragmentar 6)|Contagem|Total||
|cachehits6|Ocorrências de cache (fragmentar 6)|Contagem|Total||
|cachemisses6|Erros de cache (fragmentar 6)|Contagem|Total||
|getcommands6|Obtém (fragmentar 6)|Contagem|Total||
|setcommands6|Conjuntos (fragmentar 6)|Contagem|Total||
|evictedkeys6|Chaves removidas (fragmentar 6)|Contagem|Total||
|totalkeys6|Total de chaves (nó 6)|Contagem|Máximo||
|expiredkeys6|Chaves expiradas (fragmentar 6)|Contagem|Total||
|usedmemory6|Memória usada (fragmentar 6)|Bytes|Máximo||
|usedmemoryRss6|Memória usada RSS (fragmentar 6)|Bytes|Máximo||
|serverLoad6|Carga do servidor (fragmentar 6)|Porcentagem|Máximo||
|cacheWrite6|Gravação de cache (fragmentar 6)|BytesPerSecond|Máximo||
|cacheRead6|Leitura de cache (fragmentar 6)|BytesPerSecond|Máximo||
|percentProcessorTime6|CPU (fragmentar 6)|Porcentagem|Máximo||
|connectedclients7|Clientes conectados (fragmentar 7)|Contagem|Máximo||
|totalcommandsprocessed7|Total de operações (fragmentar 7)|Contagem|Total||
|cachehits7|Ocorrências de cache (fragmentar 7)|Contagem|Total||
|cachemisses7|Erros de cache (fragmentar 7)|Contagem|Total||
|getcommands7|Obtém (fragmentar 7)|Contagem|Total||
|setcommands7|Conjuntos (fragmentar 7)|Contagem|Total||
|evictedkeys7|Chaves removidas (fragmentar 7)|Contagem|Total||
|totalkeys7|Total de chaves (nó 7)|Contagem|Máximo||
|expiredkeys7|Chaves expiradas (fragmentar 7)|Contagem|Total||
|usedmemory7|Memória usada (fragmentar 7)|Bytes|Máximo||
|usedmemoryRss7|Memória usada RSS (fragmentar 7)|Bytes|Máximo||
|serverLoad7|Carga do servidor (fragmentar 7)|Porcentagem|Máximo||
|cacheWrite7|Gravação de cache (fragmentar 7)|BytesPerSecond|Máximo||
|cacheRead7|Leitura de cache (fragmentar 7)|BytesPerSecond|Máximo||
|percentProcessorTime7|CPU (fragmentar 7)|Porcentagem|Máximo||
|connectedclients8|Clientes conectados (fragmentar 8)|Contagem|Máximo||
|totalcommandsprocessed8|Total de operações (fragmentar 8)|Contagem|Total||
|cachehits8|Ocorrências de cache (fragmentar 8)|Contagem|Total||
|cachemisses8|Erros de cache (fragmentar 8)|Contagem|Total||
|getcommands8|Obtém (fragmentar 8)|Contagem|Total||
|setcommands8|Conjuntos (fragmentar 8)|Contagem|Total||
|evictedkeys8|Chaves removidas (fragmentar 8)|Contagem|Total||
|totalkeys8|Total de chaves (nó 8)|Contagem|Máximo||
|expiredkeys8|Chaves expiradas (fragmentar 8)|Contagem|Total||
|usedmemory8|Memória usada (fragmentar 8)|Bytes|Máximo||
|usedmemoryRss8|Memória usada RSS (fragmentar 8)|Bytes|Máximo||
|serverLoad8|Carga do servidor (fragmentar 8)|Porcentagem|Máximo||
|cacheWrite8|Gravação de cache (fragmentar 8)|BytesPerSecond|Máximo||
|cacheRead8|Leitura de cache (fragmentar 8)|BytesPerSecond|Máximo||
|percentProcessorTime8|CPU (fragmentar 8)|Porcentagem|Máximo||
|connectedclients9|Clientes conectados (fragmentar 9)|Contagem|Máximo||
|totalcommandsprocessed9|Total de operações (fragmentar 9)|Contagem|Total||
|cachehits9|Ocorrências de cache (fragmentar 9)|Contagem|Total||
|cachemisses9|Erros de cache (fragmentar 9)|Contagem|Total||
|getcommands9|Obtém (fragmentar 9)|Contagem|Total||
|setcommands9|Conjuntos (fragmentar 9)|Contagem|Total||
|evictedkeys9|Chaves removidas (fragmentar 9)|Contagem|Total||
|totalkeys9|Total de chaves (nó 9)|Contagem|Máximo||
|expiredkeys9|Chaves expiradas (fragmentar 9)|Contagem|Total||
|usedmemory9|Memória usada (fragmentar 9)|Bytes|Máximo||
|usedmemoryRss9|Memória usada RSS (fragmentar 9)|Bytes|Máximo||
|serverLoad9|Carga do servidor (fragmentar 9)|Porcentagem|Máximo||
|cacheWrite9|Gravação de cache (fragmentar 9)|BytesPerSecond|Máximo||
|cacheRead9|Leitura de cache (fragmentar 9)|BytesPerSecond|Máximo||
|percentProcessorTime9|CPU (fragmentar 9)|Porcentagem|Máximo||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|NumberOfCalls|Número total de chamadas de API|Contagem|Total|Número total de chamadas API.|
|NumberOfFailedCalls|Número total de chamadas de API falhou|Contagem|Total|Número total de falhas de chamadas de API.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem de unidades de computação alocado que estão atualmente em uso por das máquinas virtuais|
|Rede na|Rede na|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede das máquinas virtuais (tráfego de entrada)|
|Rede-Out|Rede-Out|Bytes|Total|O número de bytes check-out em todas as interfaces de rede das máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravadas em disco durante o período de monitoramento|
|Operações de leitura/s de disco|Operações de leitura/s de disco|CountPerSecond|Média|Leitura de disco IOPS|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem de unidades de computação alocado que estão atualmente em uso por das máquinas virtuais|
|Rede na|Rede na|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede das máquinas virtuais (tráfego de entrada)|
|Rede-Out|Rede-Out|Bytes|Total|O número de bytes check-out em todas as interfaces de rede das máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravadas em disco durante o período de monitoramento|
|Operações de leitura/s de disco|Operações de leitura/s de disco|CountPerSecond|Média|Leitura de disco IOPS|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem de unidades de computação alocado que estão atualmente em uso por das máquinas virtuais|
|Rede na|Rede na|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede das máquinas virtuais (tráfego de entrada)|
|Rede-Out|Rede-Out|Bytes|Total|O número de bytes check-out em todas as interfaces de rede das máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravadas em disco durante o período de monitoramento|
|Operações de leitura/s de disco|Operações de leitura/s de disco|CountPerSecond|Média|Leitura de disco IOPS|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de dispositivo para nuvem mensagens de telemetria tentou ser enviadas para seu hub IoT|
|d2c.telemetry.ingress.Success|Telemetria mensagens enviadas|Contagem|Total|Número de dispositivo a nuvem telemetria mensagens enviadas com êxito para o seu hub IoT|
|c2d.Commands.egress.Complete.Success|Comandos concluídos|Contagem|Total|Número de nuvem para comandos de dispositivo foi concluída com êxito pelo dispositivo|
|c2d.Commands.egress.Abandon.Success|Comandos abandonados|Contagem|Total|Número de nuvem para comandos de dispositivo abandonados pelo dispositivo|
|c2d.Commands.egress.Reject.Success|Comandos rejeitados|Contagem|Total|Número de nuvem para comandos de dispositivo rejeitada pelo dispositivo|
|devices.totalDevices|Dispositivos totais|Contagem|Total|Número de dispositivos registrado para seu hub IoT|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Contagem|Total|Número de dispositivos conectados ao seu hub IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|INREQS|Solicitações de entrada|Contagem|Total|Evento Hub entrada mensagem produtividade para um namespace|
|SUCCREQ|Solicitações com êxito|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|
|FAILREQ|Solicitações com falha|Contagem|Total|Total de solicitações com falha para um namespace|
|SVRBSY|Erros de ocupado do servidor|Contagem|Total|Erros de ocupado total do servidor para um namespace|
|INTERR|Erros do servidor interno|Contagem|Total|Erros de total do servidor interno para um namespace|
|MISCERR|Outros erros|Contagem|Total|Total de solicitações com falha para um namespace|
|INMSGS|Mensagens de entrada|Contagem|Total|Total de mensagens recebidas para um namespace|
|OUTMSGS|Mensagens de saída|Contagem|Total|Total de mensagens para um namespace de saída|
|EHINMBS|Bytes recebidas por segundo|BytesPerSecond|Total|Evento Hub entrada mensagem produtividade para um namespace|
|EHOUTMBS|Bytes de saída por segundo|BytesPerSecond|Total|Total de mensagens para um namespace de saída|
|EHABL|Arquivar mensagens de registro posterior|Contagem|Total|Hub arquivar mensagens de evento registros acumulados para um namespace|
|EHAMSGS|Arquivar mensagens|Contagem|Total|Hub de evento arquivados mensagens em um namespace|
|EHAMBS|Taxa de transferência de mensagens de arquivamento|BytesPerSecond|Total|Taxa de transferência de mensagem arquivada Hub de evento em um namespace|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|RunsStarted|Executa iniciada|Contagem|Total|Número de fluxo de trabalho é executado iniciado.|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de fluxo de trabalho é executado concluído.|
|RunsSucceeded|Executa bem-sucedida|Contagem|Total|Número de fluxo de trabalho é executado com êxito.|
|RunsFailed|Executa falhou|Contagem|Total|Número de fluxo de trabalho é executado com falha.|
|RunsCancelled|Executa cancelada|Contagem|Total|Número de fluxo de trabalho é executado cancelado.|
|RunLatency|Executar latência|Segundos|Média|Latência de fluxo de trabalho concluído é executado.|
|RunSuccessLatency|Executar a latência de sucesso|Segundos|Média|Latência de fluxo de trabalho bem-sucedida é executado.|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ação de fluxo de trabalho ou disparador limitada eventos.|
|RunFailurePercentage|Executar a porcentagem de falha|Porcentagem|Total|Porcentagem de fluxo de trabalho é executado com falha.|
|ActionsStarted|Ações de Introdução |Contagem|Total|Número de ações de fluxo de trabalho é iniciado.|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluído.|
|ActionsSucceeded|Ações bem-sucedida |Contagem|Total|Número de ações de fluxo de trabalho com êxito.|
|ActionsFailed|Falha de ações|Contagem|Total|Número de ações de fluxo de trabalho falha.|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignorada.|
|ActionLatency|Latência de ação |Segundos|Média|Latência de ações de fluxo de trabalho concluído.|
|ActionSuccessLatency|Latência de sucesso de ação |Segundos|Média|Latência de ações de fluxo de trabalho com êxito.|
|ActionThrottledEvents|Ação limitado eventos|Contagem|Total|Número de ação de fluxo de trabalho controlado eventos..|
|TriggersStarted|Gatilhos de Introdução |Contagem|Total|Número de gatilhos de fluxo de trabalho é iniciado.|
|TriggersCompleted|Disparadores concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluído.|
|TriggersSucceeded|Disparadores bem-sucedida |Contagem|Total|Número de gatilhos de fluxo de trabalho com êxito.|
|TriggersFailed|Falha de disparadores |Contagem|Total|Número de gatilhos de fluxo de trabalho falha.|
|TriggersSkipped|Disparadores ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorada.|
|TriggersFired|Disparadores acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionado.|
|TriggerLatency|Latência de disparadores |Segundos|Média|Latência de disparadores de fluxo de trabalho concluído.|
|TriggerFireLatency|Latência de Fire disparadores |Segundos|Média|Latência de acionado gatilhos de fluxo de trabalho.|
|TriggerSuccessLatency|Latência de sucesso do disparador |Segundos|Média|Latência de bem-sucedida gatilhos de fluxo de trabalho.|
|TriggerThrottledEvents|Disparar eventos limitados|Contagem|Total|Número de gatilho de fluxo de trabalho limitada eventos.|
|BillableActionExecutions|Execuções de ação faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho obtendo cobrado.|
|BillableTriggerExecutions|Execuções disparador faturáveis|Contagem|Total|Número de fluxo de trabalho disparador execuções obtendo cobrado.|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho obtendo cobrado.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Taxa de transferência|Taxa de transferência|BytesPerSecond|Média||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|
|ThrottledSearchQueriesPercentage|Porcentagem de consultas de pesquisa limitado|Porcentagem|Média|Porcentagem de consultas de pesquisa que foram limitado para o serviço de pesquisa|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CPUXNS|Uso de CPU por namespace|Porcentagem|Máximo|Métrica de uso do serviço barramento premium namespace da CPU|
|WSXNS|Uso de tamanho de memória por namespace|Porcentagem|Máximo|Métrica de uso de memória do serviço barramento premium namespace|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|
|physical_data_read_percent|Porcentagem de IO de dados|Porcentagem|Média|Porcentagem de IO de dados|
|log_write_percent|Porcentagem de IO de log|Porcentagem|Média|Porcentagem de IO de log|
|dtu_consumption_percent|Porcentagem DTU|Porcentagem|Média|Porcentagem DTU|
|armazenamento|Tamanho total do banco de dados|Bytes|Máximo|Tamanho total do banco de dados|
|connection_successful|Conexões bem-sucedidas|Contagem|Total|Conexões bem-sucedidas|
|connection_failed|Falha de conexões|Contagem|Total|Falha de conexões|
|blocked_by_firewall|Bloqueado pelo Firewall|Contagem|Total|Bloqueado pelo Firewall|
|bloqueio|Travamentos|Contagem|Total|Travamentos|
|storage_percent|Porcentagem de tamanho de banco de dados|Porcentagem|Máximo|Porcentagem de tamanho de banco de dados|
|xtp_storage_percent|Percent(Preview) de armazenamento de memória OLTP|Porcentagem|Média|Percent(Preview) de armazenamento de memória OLTP|
|workers_percent|Porcentagem de trabalhadores|Porcentagem|Média|Porcentagem de trabalhadores|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|
|dtu_limit|Limite DTU|Contagem|Média|Limite DTU|
|dtu_used|DTU usado|Contagem|Média|DTU usado|
|service_level_objective|Objetivo de nível de serviço do banco de dados|Contagem|Total|Objetivo de nível de serviço do banco de dados|
|dwu_limit|limite de dwu|Contagem|Máximo|limite de dwu|
|dwu_consumption_percent|Porcentagem DWU|Porcentagem|Média|Porcentagem DWU|
|dwu_used|DWU usado|Contagem|Média|DWU usado|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|
|physical_data_read_percent|Porcentagem de IO de dados|Porcentagem|Média|Porcentagem de IO de dados|
|log_write_percent|Porcentagem de IO de log|Porcentagem|Média|Porcentagem de IO de log|
|dtu_consumption_percent|Porcentagem DTU|Porcentagem|Média|Porcentagem DTU|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|
|workers_percent|Porcentagem de trabalhadores|Porcentagem|Média|Porcentagem de trabalhadores|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|
|eDTU_limit|limite de eDTU|Contagem|Média|limite de eDTU|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|ResourceUtilization|% De utilização do SO|Porcentagem|Máximo|% De utilização do SO|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|
|LateInputEvents|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|
|ConversionErrors|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|
|Erros|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|
|DroppedOrAdjustedEvents|Eventos de fora de ordem|Contagem|Total|Eventos de fora de ordem|
|AMLCalloutRequests|Solicitações de função|Contagem|Total|Solicitações de função|
|AMLCalloutFailedRequests|Solicitações de função falhou|Contagem|Total|Solicitações de função falhou|
|AMLCalloutInputEvents|Eventos de função|Contagem|Total|Eventos de função|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuPercentage|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|
|MemoryPercentage|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|
|DiskQueueLength|Comprimento da fila de disco|Contagem|Total|Comprimento da fila de disco|
|HttpQueueLength|Comprimento da fila de http|Contagem|Total|Comprimento da fila de http|
|BytesReceived|Dados em|Bytes|Total|Dados em|
|BytesSent|Dados|Bytes|Total|Dados|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Solicitações|Solicitações|Contagem|Total|Solicitações|
|BytesReceived|Dados em|Bytes|Total|Dados em|
|BytesSent|Dados|Bytes|Total|Dados|
|Http2xx|2xx http|Contagem|Total|2xx http|
|Http3xx|3xx http|Contagem|Total|3xx http|
|Http401|HTTP 401|Contagem|Total|HTTP 401|
|Http403|HTTP 403|Contagem|Total|HTTP 403|
|Http404|HTTP 404|Contagem|Total|HTTP 404|
|Http406|HTTP 406|Contagem|Total|HTTP 406|
|Http4xx|4xx http|Contagem|Total|4xx http|
|Http5xx|Erros de servidor HTTP|Contagem|Total|Erros de servidor HTTP|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Total|Conjunto de trabalho de memória|
|AverageMemoryWorkingSet|Média memória conjunto de trabalho|Bytes|Média|Média memória conjunto de trabalho|
|AverageResponseTime|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome para exibição métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Solicitações|Solicitações|Contagem|Total|Solicitações|
|BytesReceived|Dados em|Bytes|Total|Dados em|
|BytesSent|Dados|Bytes|Total|Dados|
|Http2xx|2xx http|Contagem|Total|2xx http|
|Http3xx|3xx http|Contagem|Total|3xx http|
|Http401|HTTP 401|Contagem|Total|HTTP 401|
|Http403|HTTP 403|Contagem|Total|HTTP 403|
|Http404|HTTP 404|Contagem|Total|HTTP 404|
|Http406|HTTP 406|Contagem|Total|HTTP 406|
|Http4xx|4xx http|Contagem|Total|4xx http|
|Http5xx|Erros de servidor HTTP|Contagem|Total|Erros de servidor HTTP|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Total|Conjunto de trabalho de memória|
|AverageMemoryWorkingSet|Média memória conjunto de trabalho|Bytes|Média|Média memória conjunto de trabalho|
|AverageResponseTime|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|

## <a name="next-steps"></a>Próximas etapas

- [Leia sobre métricas no Monitor do Azure](./monitoring-overview.md#monitoring-sources)
- [Criar alertas em métricas](./insights-receive-alert-notifications.md)
- [Exportar métricas de armazenamento, Hub de evento ou a análise de Log](./monitoring-overview-of-diagnostic-logs.md)
