> [AZURE.SELECTOR]
- [Node. js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introdução

Na [guia de Introdução do IoT Hub gêmeos][lnk-twin-tutorial], você aprendeu como definir o dispositivo metadados de sua solução back-end usando *marcas*, condições de dispositivo do relatório de um aplicativo de dispositivo usando *propriedades relatadas*e essas informações usando uma linguagem de SQL de consulta.

Neste tutorial, você vai aprender como usar as *propriedades desejadas* do twin em conjunto com *relatado propriedades*, para configurar remotamente os aplicativos de dispositivo. Mais especificamente, este tutorial mostra como twin relatado e propriedades desejadas habilitar uma configuração de várias etapa de uma configuração de aplicativo do dispositivo e fornecem a visibilidade ao back-end solução do status dessa operação entre todos os dispositivos.

Em um alto nível, este tutorial segue o *padrão de estado desejado* para o gerenciamento de dispositivos. A ideia fundamental desse padrão é ter o back-end de solução especificar estado desejado para os dispositivos gerenciados, em vez de enviar comandos específicos. Coloca o dispositivo responsável por estabelecer a melhor maneira de acessar o estado desejado (muito importante em cenários de IoT quais condições específicas de dispositivo afetam a capacidade de realizar imediatamente comandos específicos), enquanto relatórios continuamente ao back-end, o estado atual e as possíveis condições de erro. O padrão de estado desejado é fundamental para o gerenciamento de grandes conjuntos de dispositivos, como ele permite que o back-end tenha total visibilidade do estado do processo de configuração em todos os dispositivos.
Você pode encontrar mais informações sobre a função do padrão de estado desejado no gerenciamento de dispositivo no [gerenciamento de dispositivos de visão geral do Windows Azure IoT Hub][lnk-dm-overview].

> [AZURE.NOTE] Em cenários onde os dispositivos são controlados de maneira mais interativa (ativar um ventilador a partir de um aplicativo controlado pelo usuário), considere o uso de [métodos de nuvem para dispositivo][lnk-methods].

Neste tutorial, as alterações de back-end do aplicativo a configuração de telemetria de um dispositivo de destino e, como resultado de que o aplicativo do dispositivo segue um processo de várias etapas para aplicar uma atualização de configuração (por exemplo, exigindo uma reinicialização do módulo de software), este tutorial simula com um atraso simple).

O back-end armazena a configuração nas propriedades desejadas do twin dispositivo da seguinte maneira:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Como as configurações podem ser objetos complexos, geralmente são atribuídas identificações exclusivas (hashes ou [GUIDs][lnk-guid]) para simplificar suas comparações.

O aplicativo do dispositivo informa sua configuração atual espelhamento a propriedade desejada **telemetryConfig** nas propriedades do reportados:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Observe como o relatada **telemetryConfig** tem uma propriedade adicional **status**, usado para relatar o estado do processo de atualização de configuração.

Quando uma nova configuração desejada é recebida, o aplicativo do dispositivo reporta uma configuração pendente, alterando as informações:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Em seguida, em algum momento posterior, o aplicativo do dispositivo irá relatar o sucesso da falha dessa operação atualizando a propriedade acima.
Observe que como back-end é capaz de, a qualquer momento, consultar o status do processo de configuração de todos os dispositivos.

Este tutorial mostra como para:

- Crie um simulado dispositivo que recebe atualizações de configuração de back-end e relatórios de várias atualizações como *Propriedades informadas* sobre o processo de atualização de configuração.
- Crie um aplicativo de back-end que atualiza a configuração desejada de um dispositivo e, em seguida, consulta o processo de atualização de configuração.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier