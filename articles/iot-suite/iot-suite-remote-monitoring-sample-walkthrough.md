<properties
 pageTitle="Monitoramento remoto pré-configurado explicação passo a passo de solução | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado solução monitoramento e remotos sua arquitetura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Monitoramento remoto pré-configurado solução explicação passo a passo

## <a name="introduction"></a>Introdução

Controle remoto IoT Suite monitoramento [pré-configurado solução] [ lnk-preconfigured-solutions] é uma implementação de uma ponta a ponta solução para várias máquinas executando em locais remotos de monitoramento. A solução combina serviços Azure chave para fornecer uma implementação genérica do cenário de negócios e você pode usá-lo como ponto de partida para sua própria implementação. Você pode [Personalizar] [ lnk-customize] a solução para atender às suas próprias necessidades de negócios específicas.

Este artigo orienta alguns dos principais elementos da solução monitoramento remoto para que você possa entender como ele funciona. Esse conhecimento ajuda você a:

- Solucione problemas na solução.
- Planeje como personalizar a solução para atender a necessidades específicas. 
- Projetar sua própria solução IoT que usa os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes lógicos da solução pré-configurado:

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurado, do dispositivo simulado representa um dispositivo refrigeração (como uma construção ar condicionado ou unidade de manuseio de ar de recurso). Quando você implanta a solução pré-configurado, você também automaticamente provisionar quatro dispositivos simulados que são executados em um [Azure WebJob][lnk-webjobs]. Os dispositivos simulados tornam mais fácil para você explorar o comportamento da solução sem a necessidade de implantar qualquer dispositivos físicos. Para implantar um dispositivo físico real, consulte o [conectar seu dispositivo e remoto monitoramento solução pré-configurada] [ lnk-connect-rm] tutorial.

Cada dispositivo simulado poderá enviar os seguintes tipos de mensagem para IoT Hub:

| Mensagem  | Descrição |
|----------|-------------|
| Inicialização  | Quando o dispositivo for iniciado, ele envia uma mensagem de **informações do dispositivo** que contém informações sobre si ao back-end. Esses dados incluem a identificação do dispositivo, os metadados de dispositivo, uma lista dos comandos suporta o dispositivo e a configuração atual do dispositivo. |
| Presença | Um dispositivo periodicamente envia uma mensagem de **presença** para relatar se o dispositivo pode detectar a presença de um sensor. |
| Telemetria | Um dispositivo periodicamente envia uma mensagem de **telemetria** que relata simulados valores para a temperatura e umidade coletados de sensores simulada do dispositivo. |


Os dispositivos simulados enviar as seguintes propriedades de dispositivo em uma mensagem de **informações do dispositivo** :

| Propriedade               |  Finalidade |
|------------------------|--------- |
| ID do dispositivo              | ID que é fornecida ou atribuído quando um dispositivo é criado na solução. |
| Fabricante           | Fabricante do dispositivo |
| Número do modelo           | Número do modelo do dispositivo |
| Número de série          | Número de série do dispositivo |
| Firmware               | Versão atual do firmware do dispositivo |
| Plataforma               | Arquitetura de plataforma do dispositivo |
| Processador              | Processador executando o dispositivo |
| Memória RAM instalada          | Quantidade de RAM instalada no dispositivo |
| Estado de ativação do hub      | Propriedade de estado de IoT Hub do dispositivo |
| Hora de criação           | Sempre que o dispositivo foi criado na solução |
| Horário da atualização           | Última vez propriedades foram atualizadas para o dispositivo |
| Latitude               | Local de Latitude do dispositivo |
| Longitude              | Local de longitude do dispositivo |

O simulator propaga essas propriedades em dispositivos simulados com valores de exemplo.  Sempre que o simulator inicializa um dispositivo simulado, o dispositivo postagens os metadados predefinidos IoT Hub. Observe como isso substitui quaisquer atualizações de metadados feitas no portal do dispositivo.


Os dispositivos simulados podem manipular os seguintes comandos enviados a partir do painel de solução por meio do hub IoT:

| Comando                | Descrição                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envia um _ping_ para o dispositivo para verificar se que ele está ativo   |
| StartTelemetry         | Inicia o dispositivo de envio de telemetria                 |
| StopTelemetry          | Parar o dispositivo de envio de telemetria             |
| ChangeSetPointTemp     | Altera o valor de ponto definido em torno da qual os dados aleatórios são gerados |
| DiagnosticTelemetry    | Aciona o simulator dispositivo para enviar um valor de telemetria adicional (externalTemp) |
| ChangeDeviceState      | Altera uma propriedade de estado estendido para o dispositivo e envia a mensagem de informações de dispositivo do dispositivo |

A confirmação de comando de dispositivo ao back-end solução é fornecida por meio do hub IoT.

## <a name="iot-hub"></a>Hub de IoT

O [hub de IoT] [ lnk-iothub] consome dados enviados dos dispositivos na nuvem e torna disponível para os trabalhos de análise de fluxo de Azure (ASA). Hub de IoT também envia comandos para seus dispositivos em nome de portal do dispositivo. Cada trabalho ASA fluxo usa um grupo separado de consumidor do IoT Hub ler o fluxo de mensagens em seus dispositivos.

## <a name="azure-stream-analytics"></a>Análise de fluxo Azure

Na solução de monitoramento remota, [A análise de fluxo Azure] [ lnk-asa] (ASA) distribui mensagens de dispositivo recebidas pelo hub do IoT para outros componentes de back-end para processamento ou armazenamento. Diferentes trabalhos ASA executam funções específicas com base no conteúdo das mensagens.

**Job 1: informações de dispositivo** filtra as mensagens de informações de dispositivo do fluxo de mensagem de entrada e envia para um ponto de extremidade do Hub de evento. Um dispositivo envia mensagens de informação do dispositivo na inicialização e em resposta a um comando **SendDeviceInfo** . Este trabalho usa a seguinte definição de consulta para identificar mensagens de **informações do dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esse trabalho envia a saída para um Hub de evento para processamento posterior.

**Trabalho 2: regras** avalia valores entradas de telemetria de temperatura e umidade em relação a limites de cada dispositivo. Valores de limite são definidos no editor de regras disponível no painel de solução. Cada par de dispositivo/valor é armazenado por carimbo de hora em um blob que a análise de fluxo lê como **Dados de referência**. O trabalho compara qualquer valor não vazio contra o limite definido para o dispositivo. Se ele exceder o ' >' condição, o trabalho gera um evento de **alarme** que indica que o limite é excedido e fornece o dispositivo, valor e valores de carimbo de hora. Este trabalho usa a seguinte definição de consulta para identificar mensagens de telemetria que devem disparar um alarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

O trabalho envia a saída para um Hub de evento para processamento posterior e salva os detalhes de cada alerta ao armazenamento de blob de onde o painel de solução pode ler as informações de alerta.

**Trabalho 3: telemetria** funciona com o fluxo de telemetria do dispositivo recebidos de duas maneiras. O primeiro envia todas as mensagens de telemetria dos dispositivos para armazenamento de blob persistente para armazenamento de longo prazo. A segunda calcula valores de umidade média, mínimo e máximo em uma janela deslizante de cinco minutos e envia esses dados para o armazenamento de blob. No painel de solução lê os dados de telemetria do armazenamento de blob para preencher os gráficos. Este trabalho usa a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Hubs de evento

Os trabalhos ASA **informações do dispositivo** e **as regras** de saída seus dados para Hubs de evento para confiavelmente encaminhar o **Processador de evento** em execução na WebJob-lo.

## <a name="azure-storage"></a>Armazenamento do Azure

A solução usa o armazenamento de blob do Microsoft Azure para manter todos os dados de telemetria bruto e resumidas dos dispositivos na solução. Painel lê os dados de telemetria do armazenamento de blob para preencher os gráficos. Para exibir os alertas, no painel lê os dados do armazenamento de blob que registros quando valores de telemetria excederam os valores de limite configurado. A solução também usa o armazenamento de blob para gravar os valores de limite definido no painel de controle.

## <a name="webjobs"></a>WebJobs

Além de hospedagem simuladores o dispositivo, os WebJobs na solução também hospedar o **Processador de evento** executado em um WebJob do Azure que mensagens de informações de dispositivo de alças e respostas de comando. Ele usa:

- Mensagens de informações do dispositivo para atualizar o registro de dispositivo (armazenado no banco de dados DocumentDB) com as informações atuais do dispositivo.
- Mensagens de resposta de comando para atualizar o histórico de comando de dispositivo (armazenado no banco de dados DocumentDB).

## <a name="documentdb"></a>DocumentDB

A solução usa um banco de dados de DocumentDB para armazenar informações sobre os dispositivos conectados à solução. Essas informações incluem o histórico de comandos enviados para dispositivos no painel e metadados de dispositivo.

## <a name="web-apps"></a>Aplicativos Web

### <a name="remote-monitoring-dashboard"></a>Painel de monitoramento remoto
Esta página no aplicativo da web usa os controles de javascript PowerBI (consulte [PowerBI-visuais repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria dos dispositivos. A solução usa o trabalho de telemetria ASA para gravar os dados de telemetria para armazenamento de blob.


### <a name="device-administration-portal"></a>Portal de administração do dispositivo

Este aplicativo web permite que você:

- Provisione um novo dispositivo. Esta ação define a identificação de dispositivo exclusivo e gera a chave de autenticação. Grava informações sobre o dispositivo do registro de identidade IoT Hub e o banco de dados de DocumentDB de solução específicas.
- Gerencie propriedades de dispositivo. Esta ação inclui visualizando as propriedades existentes e atualizando com novas propriedades.
- Envie comandos para um dispositivo.
- Exiba o histórico de comando para um dispositivo.
- Ativar e desativar dispositivos.

## <a name="next-steps"></a>Próximas etapas

As postagens de blog TechNet seguintes fornecem mais detalhes sobre a solução de pré-configurado monitoramento remota:

- [Monitoramento remoto do IoT Suite - nos bastidores-](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [Pacote de IoT - monitoramento remoto - adicionando dispositivos ao vivo e simulados](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Você pode continuar a introdução ao Suite IoT lendo os seguintes artigos:

- [Conectar o dispositivo à solução pré-configurado monitoramento remota][lnk-connect-rm]
- [Permissões no site azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md