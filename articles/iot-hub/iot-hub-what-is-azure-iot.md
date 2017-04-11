<properties
 pageTitle="Azure soluções para Internet das coisas | Microsoft Azure"
 description="Uma visão geral das IoT no Azure, incluindo uma arquitetura de solução de amostra e como ele se relaciona a Azure IoT Hub, SDKs do dispositivo e soluções pré-configuradas"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Próximas etapas

Hub de IoT Azure é um serviço Azure que permite seguro e confiável comunicação bidirecional entre seu aplicativo back-end e milhões de dispositivos. Ele permite que o back-end de aplicativo para:

- Receba telemetria em escala de seus dispositivos.
- Rotear dados de seus dispositivos para um processador de evento do fluxo.
- Receba carregamentos de arquivo de dispositivos.
- Envie comandos de nuvem para dispositivo para dispositivos específicos.

Você pode usar IoT Hub para implementar seu próprio back-end de solução. Além disso, o Hub de IoT inclui um registro de identidade do dispositivo usado para provisionar dispositivos, suas credenciais de segurança e seus direitos para se conectar ao hub. Para saber mais sobre o Hub de IoT, consulte [o que é o Hub de IoT?] [lnk-iot-hub].

Para saber como o Hub de IoT Azure permite gerenciamento de dispositivo de IoT baseada em padrões para você gerenciar, configurar e atualizar seus dispositivos remotamente, consulte [gerenciamento de dispositivo de visão geral do Azure IoT Hub][lnk-device-management].

Para implementar aplicativos cliente em uma ampla variedade de plataformas de hardware do dispositivo e sistemas operacionais, você pode usar o dispositivo IoT SDKs. O dispositivo de IoT SDKs incluem bibliotecas que facilitam o envio telemetria a um hub IoT e receber comandos de nuvem para dispositivo. Quando você usa o SDK do, você pode escolher entre vários protocolos de rede para se comunicar com IoT Hub. Para saber mais, consulte as [informações sobre dispositivo SDKs][lnk-device-sdks].

Para começar a escrever algum código e executando alguns exemplos, consulte a [Introdução ao IoT Hub] [ lnk-getstarted] tutorial.

Você também pode estar interessado em [Azure IoT Suite][lnk-iot-suite], que é uma coleção de soluções pré-configurado. IoT Suite permite que você a começar rapidamente e o dimensionamento IoT projetos para cenários comuns de IoT – como monitoramento remoto, gerenciamento de ativos e manutenção de previsão.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md