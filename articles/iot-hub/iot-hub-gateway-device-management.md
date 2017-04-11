<properties
 pageTitle="Habilitar dispositivos gerenciados por trás de um gateway IoT | Microsoft Azure"
 description="Tópico de orientação usando um IoT Gateway criado usando o SDK do Gateway IoT juntamente com dispositivos gerenciados pelo IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Habilitar dispositivos gerenciados por trás de um gateway IoT

## <a name="basic-device-isolation"></a>Isolamento de dispositivo básicos

Organizações geralmente usam IoT gateways para aumentar a segurança de suas soluções IoT geral. Alguns dispositivos precisam enviar dados para a nuvem, mas não são capazes de proteger-se contra ameaças na internet. Você pode proteger esses dispositivos de threads externos fazendo-los a se comunicar com o mundo exterior por meio de um gateway.

O gateway fica na borda entre um ambiente seguro e a internet abertas. Dispositivos falam com o gateway e o gateway passa as mensagens ao longo para o destino de nuvem correto. O gateway está protegido contra threads externos, bloqueia solicitações não autorizadas, permite tráfego em ligadas autorizado e encaminha esse tráfego em ligadas ao dispositivo correto.

![][1]

Você também pode colocar os dispositivos que podem se proteger atrás de um gateway para uma camada adicional de segurança. Neste cenário, você precisa apenas manter o sistema operacional corrigida contra as vulnerabilidades mais recentes, em vez de atualizar o sistema operacional em todos os dispositivos de gateway.

## <a name="isolation-plus-intelligence"></a>Isolamento além de inteligência de dados

Um roteador protegido é um gateway suficiente para isolar simplesmente dispositivos. No entanto, as soluções IoT geralmente exigem que um gateway fornece mais inteligência que simplesmente isolar dispositivos. Por exemplo, talvez você queira gerencie seus dispositivos da nuvem. Você é capaz de usar [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), um protocolo de gerenciamento de dispositivo padrão, para a parte de gerenciamento de nuvem da solução. No entanto, os dispositivos enviam telemetria usando um TCP/IP não habilitado o protocolo. Além disso, os dispositivos produzem grandes quantidades de dados e você deseja carregar um subconjunto filtrado da telemetria. Você pode criar uma solução que incorpora um gateway IoT capaz de lidar com dois distintos fluxos de dados. O gateway deve:

-   Entender a **telemetria**, filtragem e carregue-o para a nuvem através do gateway. O gateway não está mais um roteador simples que simplesmente encaminha dados entre o dispositivo e a nuvem.

-   Basta troca os **dados de gerenciamento de dispositivo de LWM2M** entre os dispositivos e a nuvem. O gateway não precisa compreender os dados provenientes nela e só precisa certificar-se de que os dados obtém passados trocadas entre os dispositivos e a nuvem.

A figura a seguir ilustra esse cenário:

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>Solução: o SDK do Gateway IoT e gerenciamento de dispositivo do Azure IoT 

O público visualiza lançamento do [gerenciamento de dispositivo do Azure IoT] [ lnk-device-management] e a versão beta do [Azure IoT Gateway SDK] habilitar esse cenário. O gateway trata cada fluxo de dados da seguinte maneira:

-   **Telemetria**: você pode usar o SDK do Gateway IoT para criar um pipeline que entende, filtros e envia dados de telemetria na nuvem. O SDK do Gateway IoT fornece código que implementa partes desse pipeline em nome do desenvolvedor. Você pode encontrar mais informações sobre a arquitetura do SDK no [SDK do Gateway IoT - Introdução] [ lnk-gateway-get-started] tutorial.

-   **Gerenciamento de dispositivo**: gerenciamento de dispositivo Azure oferece um cliente de LWM2M que é executada no dispositivo, bem como uma interface de nuvem para emitir comandos de gerenciamento no dispositivo.
    
    Você não exige qualquer lógica especial no gateway porque ele não precisa processar os dados de LWM2M trocados entre o dispositivo e o seu hub IoT. Você pode habilitar a conexão de internet compartilhamento, um recurso de muitos sistemas operacionais modernos, no gateway para habilitar a troca de dados de LWM2M. Você pode escolher um sistema operacional adequado para esse cenário porque o SDK do Gateway IoT dá suporte a uma variedade de sistemas operacionais. Aqui estão instruções para ativar a conexão de internet compartilhamento no [Windows 10] e [Ubuntu], dois dos vários sistemas operacionais compatíveis.

A ilustração a seguir mostra a arquitetura de alto nível usada para habilitar esse cenário usando o [gerenciamento de dispositivos do Azure IoT] [ lnk-device-management] e o [SDK do Azure IoT Gateway].

![][3]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o SDK do Gateway IoT, consulte os seguintes tutoriais:

- [IoT Gateway SDK - começar a usar o Linux][lnk-gateway-get-started]
- [IoT Gateway SDK – enviar mensagens de dispositivo na nuvem com um dispositivo simulado usando o Linux][lnk-gateway-simulated]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Gateway do Azure IoT SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md