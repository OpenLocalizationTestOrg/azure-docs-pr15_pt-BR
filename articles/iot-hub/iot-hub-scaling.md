<properties
 pageTitle="Hub de IoT Azure dimensionamento | Microsoft Azure"
 description="Descreve como dimensionar Azure IoT Hub."
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
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Dimensionamento IoT Hub

Hub de IoT Azure pode oferecer suporte a até um milhão de dispositivos conectados simultaneamente. Para obter mais informações, consulte [IoT Hub preços][lnk-pricing]. Cada unidade de IoT Hub permite que um determinado número de mensagens de diários.

Para dimensionar corretamente sua solução, considere a possibilidade de seu uso específico do IoT Hub. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens de dispositivo à nuvem
* Mensagens de nuvem para dispositivo
* Operações de registro de identidade

Além dessas informações de produtividade, consulte [limitações de IoT Hub cotas][] e projetar sua solução de acordo.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Taxa de transferência de mensagem dispositivo na nuvem e nuvem para dispositivo

A melhor maneira de dimensionar uma solução IoT Hub é avaliar o tráfego em uma base por unidade.

Mensagens de dispositivo à nuvem siga estas diretrizes de produtividade contínuo.

| Camada | Produtividade contínua | Taxa de envio contínuo |
| ---- | -------------------- | ------------------- |
| S1 | Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) | Média de mensagens/minuto 278 por unidade<br/>(400.000 mensagens/dia por unidade) |
| S2 | Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) | Média de mensagens/minuto 4167 por unidade<br/>(6 milhões mensagens/dia por unidade) |
| S3 | Até 814 MB/minuto por unidade<br/>(1144.4 GB/dia/unidade) | Média de mensagens/minuto 208,333 por unidade<br/>(300 milhões mensagens/dia por unidade) |

## <a name="identity-registry-operation-throughput"></a>Transferência de operação de registro de identidade

Operações de registro do IoT Hub identidade não devem para ser operações de tempo de execução, conforme eles principalmente relacionados ao provisionar o dispositivo.

Para números de desempenho de intermitente específicos, consulte [limitações e cotas de IoT Hub][].

## <a name="sharding"></a>Fragmentação

Embora um único hub IoT pode dimensionar para milhões de dispositivos, às vezes, sua solução requer características específicas de desempenho que um único hub IoT não garante. Nesse caso, é recomendável que você partição seus dispositivos em vários hubs IoT. Vários hubs IoT suaves explosões de tráfego e obtém a taxa de transferência necessária ou taxas de operação necessários.

## <a name="next-steps"></a>Próximas etapas

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Limitações de cotas IoT Hub]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
