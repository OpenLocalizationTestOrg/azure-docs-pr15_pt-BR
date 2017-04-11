<properties
 pageTitle="Comparar o Azure Hub IoT aos Hubs de evento Azure | Microsoft Azure"
 description="Uma comparação dos serviços do Azure IoT Hub e Hubs de evento do Azure realce diferenças funcionais e casos de uso."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparação de Hub de IoT Azure e Hubs de evento Azure

Um dos casos uso principal para IoT Hub é reunir telemetria de dispositivos. Por esse motivo, o Hub de IoT muitas vezes é comparado com [Hubs de evento do Azure][]. Como o Hub de IoT, Hubs de evento for um evento de processamento de serviço que permite que o evento e telemetria ingresso na nuvem grande, em escala com alta confiabilidade e baixa latência.

Entretanto, os serviços têm muitas diferenças, que são detalhadas na tabela a seguir.

| Área | Hub de IoT | Hubs de evento |
| ---- | ------- | ---------- |
| Padrões de comunicação | Habilita o dispositivo na nuvem e nuvem para dispositivo de mensagens. | Só habilita o ingresso de evento (geralmente considerado para cenários de dispositivo à nuvem). |
| Suporte ao protocolo de dispositivo | Suporta MQTT, AMQP, AMQP sobre WebSocket e HTTP. Além disso, IoT Hub funciona com o [gateway de protocolo do Azure IoT][lnk-azure-protocol-gateway], uma implementação de gateway de protocolo personalizáveis para dar suporte a protocolos personalizados. | Suporta AMQP, AMQP WebSocket e HTTP sobre. |
| Segurança | Fornece identidade por dispositivo e controle de acesso revogável. Consulte a [seção de segurança do guia de desenvolvedor IoT Hub]. | Fornece toda a Hubs evento [políticas de acesso compartilhado][Event Hubs - security], com revogação limitada suporte por meio de [políticas do publisher][Event Hubs publisher policies]. Soluções de IoT geralmente são necessários para implementar uma solução personalizada para oferecer suporte a credenciais de cada dispositivo e medidas antifalsificação. |
| Operações de monitoramento | Habilita soluções IoT assine um conjunto sofisticado de eventos de conectividade e gerenciamento de identidade de dispositivo como erros de autenticação do dispositivo individual, a otimização e exceções de formato incorreto. Esses eventos permitem que você identifique rapidamente os problemas de conectividade no nível do dispositivo individual. | Expõe apenas agregadas métricas. |
| Escala | É otimizado para o suporte milhões de dispositivos conectados simultaneamente. | Pode oferecer suporte a um número limitado de mais de conexões simultâneas – até 5.000 conexões de AMQP, acordo com as [cotas de barramento de serviço do Azure][]. Por outro lado, Hubs de evento permite especificar a partição para cada mensagem enviada. |
| SDKs de dispositivo | Fornece [dispositivo SDKs] [ Azure IoT Hub SDKs] para uma grande variedade de plataformas e idiomas. | É suportado no .NET e C. Também fornece interfaces de enviar AMQP e HTTP. |
| Carregamento de arquivo | Habilita IoT soluções de carregar arquivos de dispositivos para a nuvem. Inclui um ponto de extremidade de notificação de arquivo para integração de fluxo de trabalho e uma categoria para suporte à depuração de monitoramento de operações. | Usa um padrão de verificação de declarar manualmente arquivos de solicitação de dispositivos e fornecer dispositivos com uma chave de armazenamento para a transação. |

Em resumo, mesmo se o único caso de uso é ingresso de telemetria do dispositivo na nuvem, o Hub de IoT fornece um serviço especificamente para conectividade de dispositivo de IoT. Ela continuará expandir as propostas de valor para esses cenários com recursos IoT específicos. Hubs de evento destina-se a entrada de evento em grande escala, tanto no contexto de cenários de data center inter e data center UE.

Não é incomum usar IoT Hub e Hubs de evento na mesma solução – onde IoT Hub cuida da comunicação de dispositivo na nuvem e Hubs de evento manipula ingresso estágio posterior evento em mecanismos de processamento em tempo real.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o planejamento de sua implantação IoT Hub, consulte [escala, HA e DR][lnk-scaling].

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[Hubs de evento Azure]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Seção de segurança do guia de desenvolvedor IoT Hub]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure cotas de barramento de serviço]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
