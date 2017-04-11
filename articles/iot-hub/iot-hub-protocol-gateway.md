<properties
   pageTitle="Gateway de protocolo IoT Azure | Microsoft Azure"
   description="Descreve como usar o gateway de protocolo IoT do Azure para estender os recursos e o suporte ao protocolo do Azure IoT Hub."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Suporte a protocolos adicionais para IoT Hub

Hub de IoT Azure nativamente oferece suporte a comunicação sobre os protocolos MQTT, AMQP e HTTP. Em alguns casos, dispositivos ou gateways de campo não podem ser capazes de usar um desses protocolos padrão em exigirão adaptação de protocolo. Nesses casos, você pode usar um gateway personalizado. Um gateway personalizado pode habilitar adaptação de protocolo para pontos de extremidade do IoT Hub ponte o tráfego para e do IoT Hub. Você pode usar o [gateway de protocolo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para habilitar adaptação de protocolo para IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Gateway de protocolo IoT Azure

O gateway de protocolo IoT Azure é uma estrutura para adaptação de protocolo que foi projetada para alta escala, comunicação de dispositivo bidirecional com IoT Hub. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo ao longo de um protocolo específico. Ele preenche o tráfego para IoT Hub AMQP 1.0. O gateway de protocolo Azure IoT está disponível como um projeto de software de código-fonte aberto para fornecer flexibilidade para adicionando suporte para uma variedade de protocolos e versões do protocolo.

Você pode implantar o gateway de protocolo no Azure de maneira altamente escalável usando funções de trabalho de serviços de nuvem do Azure. Além disso, o gateway de protocolo pode ser implantado em ambientes locais, como gateways de campo.

O gateway de protocolo Azure IoT inclui um adaptador de protocolo MQTT que permite que você personalize o comportamento de protocolo MQTT, se necessário. Como o IoT Hub oferece suporte interno para o protocolo de v 3.1.1 MQTT, você deve considerar apenas usando o adaptador de protocolo MQTT se você tiver uma necessidade de personalizações de protocolo ou requisitos específicos para funcionalidade adicional.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do Azure IoT protocolo gateway permite que você conecte componentes personalizados para processamento especializado como autenticação personalizada, as transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e IoT Hub.

Para flexibilidade, o gateway de protocolo e a implementação de MQTT são fornecidos em um projeto de software de código-fonte aberto. Isso permite que você personalize a implementação conforme necessário.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução IoT, consulte:

* [Azure IoT protocolo gateway repositório GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia de desenvolvedor do gateway do Azure IoT protocolo](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre o planejamento de sua implantação IoT Hub, consulte:

- [Comparar com Hubs de evento][lnk-compare]
- [Dimensionamento, HA e DR][lnk-scaling]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
