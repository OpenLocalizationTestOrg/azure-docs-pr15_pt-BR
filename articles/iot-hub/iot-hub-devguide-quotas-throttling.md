<properties
 pageTitle="Guia Desenvolvedor - cotas e a otimização | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - descrição do cotas que se aplicam a IoT Hub e limitação de comportamento esperado"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="reference---quotas-and-throttling"></a>Referência - cotas e otimização

## <a name="quotas-and-throttling"></a>Cotas e otimização

Cada assinatura Azure pode ter no máximo 10 hubs de IoT.

Cada hub IoT está provisionado com um determinado número de unidades em uma SKU específica (para obter mais informações, consulte [Preços do Azure IoT Hub][lnk-pricing]). O SKU e o número de unidades determinam a cota diária máximo das mensagens que você pode enviar.

O SKU também determina os limites de otimização que IoT Hub impõe em todas as operações.

## <a name="operation-throttles"></a>Limitações de operação

Limitações de operação são limitações de taxa que são aplicadas em intervalos de minuto e destinam-se evitar abuse. Hub de IoT tenta evitar retornando erros sempre que possível, mas ele começa retornando exceções se a aceleração foi violada por muito tempo.

A seguir é a lista de Aceleradores impostas. Valores referem-se a um hub individual.

| Aceleração | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade (criar, recuperar, lista, atualizar, excluir) | min/5000/unidade (para S3) <br/> 100/min/unidade (S1 e S2). |
| Conexões de dispositivo | sec/6000/unidade (para S3), 120/seg/unidade (S2), s/12/unidade (para S1). <br/>Mínimo de 100/seg. <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24/sec, mas você tem pelo menos de 100/seg em suas unidades. Com nove unidades de S1, você tem 108/seg (9\*12) em suas unidades. |
| Envia dispositivo à nuvem | sec/6000/unidade (para S3), 120/seg/unidade (S2), s/12/unidade (para S1). <br/>Mínimo de 100/seg. <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24/sec, mas você tem pelo menos de 100/seg em suas unidades. Com nove unidades de S1, você tem 108/seg (9\*12) em suas unidades. |
| Envia de nuvem para dispositivo | min/5000/unidade (para S3), 100/min/unidade (S1 e S2). |
| Nuvem para dispositivo recebe | min/50000/unidade (para S3), 1000/min/unidade (S1 e S2). |
| Operações de carregamento de arquivo | arquivo de 5000 carregar min/notificações/unidade (para S3), carregamento de arquivo 100 notificações/min/unidade (para S1 e S2). <br/> 10000 URIs de SAS pode ser check-out para uma conta de armazenamento do Azure ao mesmo tempo.<br/> 10 SAS URIs/dispositivo pode ser check-out de uma só vez. | 

É importante esclarecer que a aceleração de *conexões de dispositivo* controla a taxa em que novas conexões de dispositivo podem ser estabelecidas com um hub de IoT e não o número máximo de dispositivos conectados simultaneamente. A aceleração depende do número de unidades que são provisionados para o hub.

Por exemplo, se você comprar uma única unidade de S1, obtenha uma aceleração de 100 conexões por segundo. Isso significa que para conectar 100.000 dispositivos, leva pelo menos de 1000 segundos (aproximadamente 16 minutos). No entanto, você pode ter quantos dispositivos simultaneamente conectados que você tiver dispositivos registrados no seu registro de identidade do dispositivo.

Para uma discussão aprofundada do IoT Hub a otimização de comportamento, consulte o postagem de blog [IoT Hub otimização e][lnk-throttle-blog].

>[AZURE.NOTE] A qualquer momento, é possível aumentar cotas ou limites de aceleração aumentando o número de unidades provisionados em um hub IoT.

>[AZURE.IMPORTANT] Operações de registro de identidade destinam-se ao uso de tempo de execução em cenários de provisionamento e gerenciamento de dispositivo. Ler ou atualizar um grande número de identidades do dispositivo é suportado através [de importação e exportação trabalhos][lnk-importexport].

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste guia de desenvolvedor do IoT Hub incluem:

- [Pontos de extremidade do IoT Hub][lnk-devguide-endpoints]
- [Linguagem de consulta para gêmeos, métodos e trabalhos][lnk-devguide-query]
- [Suporte de IoT Hub MQTT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md