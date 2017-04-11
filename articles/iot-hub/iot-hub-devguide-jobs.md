<properties
 pageTitle="Guia do desenvolvedor - trabalhos | Microsoft Azure"
 description="Guia do desenvolvedor do IoT Hub Azure - agendar trabalhos seja executado em vários dispositivos conectados ao seu hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Agendar trabalhos em vários dispositivos (prévia)

## <a name="overview"></a>Visão geral

Conforme descrito por artigos anteriores, Azure IoT Hub permite que um número de blocos de construção ([marcas e dispositivo twin propriedades] [ lnk-twin-devguide] e [métodos de nuvem para dispositivo][lnk-dev-methods]).  Normalmente, aplicativos de back-end IoT permitem que os administradores de dispositivo e operadores atualizar e interagir com dispositivos de IoT em massa e em um horário agendado.  Trabalhos encapsulam a execução de dispositivo twin atualizações e métodos de C2D em relação a um conjunto de dispositivos por vez cronograma.  Por exemplo, um operador usaria um aplicativo de back-end que deseja iniciar e controlar um trabalho para reinicializar um conjunto de dispositivos em criar 43 e andar 3 cada vez que não seria interrupções às operações do edifício.

### <a name="when-to-use"></a>Quando usar

Considere a possibilidade de usar os trabalhos ao: solução back-end precisa agendar e controlar o andamento qualquer uma das seguintes atividades em um conjunto de dispositivo:

- Atualizar as propriedades de twin desejado de dispositivo
- Atualizar as marcas de twin de dispositivo
- Chamar métodos de C2D

## <a name="job-lifecycle"></a>Ciclo de vida do trabalho

Trabalhos são iniciados pelo back-end solução e mantidos pelo IoT Hub.  Você pode iniciar um trabalho por meio de um URI voltados para o serviço (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) e Consultar andamento em um trabalho em execução por meio de um URI voltados para o serviço (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Quando um trabalho é iniciado, consultando para trabalhos permitirá que o aplicativo de back-end atualizar o status de trabalhos em execução.

> [AZURE.NOTE] Quando você inicia um trabalho, valores e nomes de propriedade só podem conter US-ASCII imprimível alfanuméricos, exceto qualquer no conjunto seguinte: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre o uso de trabalhos.

## <a name="jobs-to-execute-direct-methods"></a>Trabalhos para executar métodos diretos

A seguir está os detalhes da solicitação de HTTP 1.1 para executar um [método direto] [ lnk-dev-methods] em um conjunto de dispositivos usando um trabalho:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Trabalhos para atualizar as propriedades de twin de dispositivo

Os detalhes da solicitação de HTTP 1.1 para atualizar propriedades do dispositivo twin usando um trabalho é o seguinte:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Consultar andamento em trabalhos

A seguir está os detalhes da solicitação de HTTP 1.1 para [Consultar para trabalhos][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
O continuationToken é fornecido da resposta.  

## <a name="jobs-properties"></a>Propriedades de trabalhos

A seguir está uma lista de propriedades e descrições correspondentes, que podem ser usadas ao consultar para trabalhos ou resultados de trabalho.

| Propriedade | Descrição |
| -------------- | -----------------|
| **jobId** | Aplicativo fornecido ID para o trabalho. |
| **hora de início** | Aplicativo fornecido a hora de início (ISO 8601) para o trabalho. |
| **hora de término** | Hub de IoT fornecido Data (ISO 8601) para quando o trabalho concluído. Válido somente depois que o trabalho atinge o estado 'concluído'. | 
| **tipo** | Tipos de trabalhos: |
| | **scheduledUpdateTwin**: um trabalho usado para atualizar um conjunto de propriedades de twin desejado ou marcas. |
| | **scheduledDeviceMethod**: um trabalho usado para chamar um método de dispositivo em um conjunto de twin. |
| **status** | Estado atual do trabalho. Valores possíveis de status: |
| | **pendente** : agendada e aguardando para ser transferidas para cima pelo serviço de trabalho. |
| | **agendada** : agendada para um horário no futuro. |
| | **executando** : trabalho atualmente ativo. |
| | **cancelado** : trabalho foi cancelado. |
| | **Falha** : falha no trabalho. |
| | **concluída** : trabalho concluído. |
| **deviceJobStatistics** | Estatísticas sobre a execução do trabalho. |

Durante a visualização, o objeto deviceJobStatistics está disponível somente depois que o trabalho está concluído.

| Propriedade | Descrição |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Número de dispositivos no trabalho. |
| **deviceJobStatistics.failedCount** | Número de dispositivos em que o trabalho falhou. |
| **deviceJobStatistics.succeededCount** | Número de dispositivos em que o trabalho foi bem-sucedido. |
| **deviceJobStatistics.runningCount** | Número de dispositivos que estão sendo executados o trabalho. |
| **deviceJobStatistics.pendingCount** | Número de dispositivos que estão pendentes para executar o trabalho. |


### <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado no tutorial IoT Hub seguinte:

- [Trabalhos de agenda e transmissão][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
