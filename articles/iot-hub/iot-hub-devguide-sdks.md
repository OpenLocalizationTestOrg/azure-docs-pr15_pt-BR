<properties
 pageTitle="Guia do desenvolvedor - IoT Hub SDKs | Microsoft Azure"
 description="Azure IoT Hub guia Desenvolvedor - informações sobre e links para vários Azure IoT Hub dispositivo e serviço SDK do."
 services="iot-hub"
 documentationCenter=""
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

# <a name="iot-hub-sdks"></a>Hub de IoT SDKs

## <a name="iot-hub-device-sdks"></a>Dispositivo de Hub de IoT SDKs

O dispositivo do Microsoft Azure IoT SDKs contêm código que facilita a construção dispositivos e aplicativos que se conectam a e são gerenciados pelo Hub de IoT Azure services.

O seguinte dispositivo IoT SDKs estão disponíveis para download do GitHub:

- [Dispositivo Azure IoT SDK para C] [ lnk-c-device-sdk] escritos em ANSI C (C99) para portabilidade e compatibilidade ampla plataforma.
- [Dispositivo Azure IoT SDK para .NET][lnk-dotnet-device-sdk]
- [Dispositivo Azure IoT SDK para Java][lnk-java-device-sdk]
- [Dispositivo Azure IoT SDK para Node][lnk-node-device-sdk]
- [Dispositivo do Microsoft Azure IoT SDK para Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar o idioma e gerentes de pacote de plataforma específica instalar binários e dependências em sua máquina de desenvolvimento.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilidade de hardware e plataformas de sistema operacional

Para obter mais informações sobre a compatibilidade SDK com dispositivos de hardware específicos, consulte o [Azure certificados para o catálogo de dispositivo IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>Hub de IoT serviço SDKs

SDK de serviço do Microsoft Azure IoT contêm código que facilita a criação de aplicativos que interagem diretamente com IoT Hub para gerenciar dispositivos e segurança.

O seguinte serviço IoT SDKs estão disponíveis para download do GitHub:

- [Serviço Azure IoT SDK para .NET][lnk-dotnet-service-sdk]
- [Serviço Azure IoT SDK para Node][lnk-node-service-sdk]
- [Serviço Azure IoT SDK para Java][lnk-java-service-sdk]

> [AZURE.NOTE] Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar o idioma e gerentes de pacote de plataforma específica instalar binários e dependências em sua máquina de desenvolvimento.

## <a name="azure-iot-gateway-sdk"></a>Gateway do Azure IoT SDK

Este SDK do Azure IoT Gateway contém a infraestrutura e módulos para criar soluções de gateway IoT. Você pode estender o SDK para criar gateways sob medidos para qualquer cenário de ponta a ponta.

Você pode baixar o [SDK do Azure IoT Gateway] [ lnk-gateway-sdk] do GitHub.

## <a name="online-api-reference-documentation"></a>Documentação de referência de API on-line

A seguir é uma lista de links para documentação de referência de API online para bibliotecas de gateway, serviço e dispositivo IoT Azure:

- [Internet das coisas (IoT) .NET][lnk-dotnet-ref]
- [Hub de IoT restante][lnk-rest-ref]
- [Dispositivo Azure IoT SDK para C][lnk-c-ref]
- [Dispositivo Azure IoT SDK para Java][lnk-java-ref]
- [Serviço Azure IoT SDK para Java][lnk-java-service-ref]
- [Dispositivo Azure IoT SDK para Node][lnk-node-ref]
- [Serviço Azure IoT SDK para Node][lnk-node-service-ref]
- [Gateway de IoT Azure SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste guia de desenvolvedor do IoT Hub incluem:

- [Pontos de extremidade do IoT Hub][lnk-devguide-endpoints]
- [Linguagem de consulta para gêmeos, métodos e trabalhos][lnk-devguide-query]
- [Cotas e otimização][lnk-devguide-quotas]
- [Suporte de IoT Hub MQTT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md