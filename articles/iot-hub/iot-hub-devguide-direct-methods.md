<properties
 pageTitle="Guia do desenvolvedor - métodos diretos | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - usar métodos diretos para invocar código em seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Invocar um método direto em um dispositivo (prévia)

## <a name="overview"></a>Visão geral

Hub de IoT permite chamar métodos em dispositivos da nuvem. Métodos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada de HTTP em que eles êxito ou falham imediatamente (após um tempo limite especificado pelo usuário) para o usuário informar o status da chamada. Isso é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi possível responder, como enviar um despertar SMS para um dispositivo se um dispositivo estiver offline (SMS sendo mais cara do que uma chamada de método).

Você pode pensar um método como uma chamada de procedimento remoto diretamente para o dispositivo. Somente os métodos que foram implementados em um dispositivo podem ser chamados da nuvem. Se a nuvem tenta invocar um método em um dispositivo que não tem esse método definido, a chamada do método falhará.

Cada método de dispositivo se destina a um único dispositivo. [Trabalhos] [ lnk-devguide-jobs] oferece uma maneira de chamar métodos em vários dispositivos e fila invocação de método para dispositivos desconectados.

Qualquer pessoa com permissões de **serviço se conectar** no IoT Hub pode invocar um método em um dispositivo.

### <a name="when-to-use"></a>Quando usar

Métodos de dispositivo são semelhantes às [mensagens de nuvem para dispositivo] [ lnk-devguide-messages] em que ambos habilitam o nuvem back-end passar informações para um dispositivo, mas eles diferem em maneiras fundamentais. Forma conceitual, métodos são síncrono e não duráveis, enquanto as mensagens de nuvem para dispositivo são assíncronas com até 48 horas de durabilidade.

Métodos seguem um padrão de solicitação-resposta e não são duráveis. A falta de durabilidade oferece dois benefícios imediatos quando você estiver comandos dispositivos:

- **Feedback imediato na execução do método** significa que não é necessário para você gerenciar a correlação entre a solicitação e resposta.
- **Maior produtividade** significa que as operações podem ser executadas com mais rapidez porque IoT Hub não está fornecendo quaisquer durabilidade. Hub de IoT permite mais chamadas de método unitário que mensagens de nuvem para dispositivo.

Mensagens de nuvem para dispositivo não são necessariamente comandos no dispositivo, mas preferir representam um serviço de nuvem passando alguns bits de informações para o dispositivo para que ele atende em seu tempo livre e para que o dispositivo pode ou não pode responder. As mensagens de nuvem para dispositivo têm mais tempo limite tempo (até 48 horas) enquanto os métodos expiram muito mais rapidamente.

Use métodos de dispositivo para invocação de comando imediato em um dispositivo e trabalhos para agendada invocação de um comando em um dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Métodos são implementados no dispositivo e podem exigir zero ou mais entradas na carga método para criar uma instância corretamente. Você invoca um método direto por meio de um URI voltados para o serviço (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos por meio de um tópico MQTT dispositivo específico (`$iothub/methods/POST/{method name}/`). Talvez suportamos métodos em protocolos de rede do lado do dispositivo adicionais no futuro.

> [AZURE.NOTE] Quando você invoca um método direto em um dispositivo, valores e nomes de propriedade só podem conter US-ASCII imprimível alfanuméricos, exceto qualquer no conjunto seguinte: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Métodos são síncronos e um sucesso ou falha após o período de tempo limite (padrão: 30 segundos, pode ser configurados para 3.600 segundos). Métodos são úteis em cenários interativos onde deseja que um dispositivo para agir somente se o dispositivo está onlinehttps e receber comandos, como ativar uma luz de um telefone. Nesses cenários, você deseja ver uma falha ou sucesso imediato para que o serviço de nuvem pode atuar no resultado assim que possível. O dispositivo pode retornar alguns corpo da mensagem como resultado do método, mas ela não é necessária para o método para fazê-lo. Não há nenhuma garantia em ordem ou qualquer semântica de concorrência em chamadas de método.

Chamadas de método de dispositivo são HTTP do lado de nuvem e somente somente MQTT do lado do dispositivo.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como usar métodos diretos.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto a partir de um aplicativo de back-end

### <a name="method-invocation"></a>Chamada do método

Chamadas de método direto em um dispositivo são chamadas HTTP que compreendem:

- O *URI* específico para o dispositivo (`{iot hub}/twins/{device id}/methods/`)
- O *método* de POST
- *Cabeçalhos* que contêm a autorização, solicitar ID, tipo de conteúdo e codificação de conteúdo
- Um transparente JSON *corpo* no seguinte formato:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Tempo limite é em segundos. Se o tempo limite não estiver definida, o padrão é 30 segundos.
  
### <a name="response"></a>Resposta

O back-end recebe uma resposta que consiste em:

- *Código de status HTTP*, que é usado para erros vêm do Hub do IoT, incluindo um erro 404 para dispositivos não conectados no momento
- *Cabeçalhos* que contêm a etag, solicitar ID, tipo de conteúdo e codificação de conteúdo
- Um JSON *corpo* no seguinte formato:

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Ambos `status` e `body` são fornecidas pelo dispositivo e usada para responder com o código de status do dispositivo e/ou a descrição.

## <a name="handle-a-direct-method-on-a-devcie"></a>Lidar com um método direto em um devcie

### <a name="method-invocation"></a>Chamada do método

Dispositivos recebem solicitações de método direto no tópico MQTT:`$iothub/methods/POST/{method name}/?$rid={request id}`

O corpo que recebe o dispositivo está no seguinte formato:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Solicitações de método são QoS 0.

### <a name="response"></a>Resposta

O dispositivo envia respostas `$iothub/methods/res/{status}/?$rid={request id}`, onde:

 - O `status` propriedade é o status fornecido pelo dispositivo de execução do método.
 - O `$rid` propriedade é a identificação de solicitação da chamada do método recebida de IoT Hub.

O corpo é definido pelo dispositivo e pode ser qualquer status.

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar métodos diretos, você pode estar interessado no tópico de guia desenvolvedor a seguir:

- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado no tutorial IoT Hub seguinte:

- [Usar métodos de nuvem para dispositivo][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
