<properties
 pageTitle="Desenvolvedor orientá - entender Gêmeos de dispositivo | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - Gêmeos de dispositivo de uso para sincronizar dados de estado e configuração entre IoT Hub e seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Entender Gêmeos de dispositivo - visualizar

## <a name="overview"></a>Visão geral

*Gêmeos de dispositivo* são documentos JSON que armazenam informações de estado de dispositivo (metadados, configurações e condições). Hub de IoT persistir twin um dispositivo para cada dispositivo que você se conecta ao IoT Hub. Este artigo descreve:

* a estrutura do twin dispositivo: *tags*, *desejado* e *relatadas propriedades*, e
* as operações que os aplicativos de dispositivo e back-ends podem realizar em Gêmeos de dispositivo.

> [AZURE.NOTE] No momento, Gêmeos de dispositivo são acessíveis somente a partir de dispositivos que se conectam a IoT Hub usando o protocolo MQTT. Consulte o [suporte de MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.

### <a name="when-to-use"></a>Quando usar

Use Gêmeos de dispositivo para:

* Armazenar meta-dados específicos do dispositivo na nuvem, por exemplo, o local de implantação de uma máquina de vendas.
* Relatar informações do estado atual como recursos disponíveis e condições de seu aplicativo de dispositivo, por exemplo, um dispositivo conectando através de celular ou wifi.
* Sincronize o estado de fluxos de trabalho de longa duração entre dispositivo aplicativo e voltar final, por exemplo, especificando a nova versão de firmware instalar back-end e o aplicativo de dispositivo os vários estágios do processo de atualização de relatório.
* Consulte seu dispositivo metadados, configuração ou estado.

Mensagens de [dispositivo à nuvem] [ lnk-d2c] para sequências de eventos de marca como série de tempo de dados ou alarmes. Usar [métodos de nuvem para dispositivo] [ lnk-methods] para controle interativo dispositivos, como ativar um ventilador.

## <a name="device-twins"></a>Gêmeos de dispositivo

Gêmeos de dispositivo armazenam informações relacionadas ao dispositivo que:

- Extremidades de dispositivo e voltar podem usar para sincronizar a configuração e condições de dispositivo.
- O back-end do aplicativo pode usar para consulta e destino demorada operações.

O ciclo de vida de um twin de dispositivo está vinculado à correspondente [dispositivo identidade][lnk-identity]. Gêmeos implicitamente são criados e excluídos quando uma nova identidade do dispositivo é criada ou excluída no IoT Hub.

Twin um dispositivo é um documento JSON que inclui:

* **Marcas**. Um documento JSON lidos e gravados pelo back-end. Marcas não estão visíveis aos aplicativos do dispositivo.
* **Propriedades de desejado**. Usado em conjunto com propriedades relatados para sincronizar a configuração de dispositivo ou condição. Propriedades desejadas só podem ser definidas pelo aplicativo volta final e podem ser lidos pelo aplicativo do dispositivo. O aplicativo de dispositivo também pode ser notificado em tempo real das alterações nas propriedades desejadas.
* **Propriedades de relatório**. Usado em conjunto com as propriedades desejadas para sincronizar a configuração de dispositivo ou condição. Propriedades relatadas só pode ser definidas pelo aplicativo do dispositivo e podem ser ler e consultadas pelo back-end do aplicativo.

Além disso, a raiz do twin dispositivo contém as propriedades somente leitura de sua identidade correspondente, contidos no [registro de identidade do dispositivo][lnk-identity].

![][img-twin]

Aqui está um exemplo de um documento do dispositivo twin JSON:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

No objeto raiz, são as propriedades do sistema e objetos de contêiner para `tags` e ambos `reported` e `desired properties`. O `properties` contêiner contém alguns elementos de somente leitura (`$metadata`, `$etag`, e `$version`) que são descritos respectivamente nos [metadados Twin] [ lnk-twin-metadata] e [concorrência otimista] [ lnk-concurrency] seções.

### <a name="reported-property-example"></a>Exemplo da propriedade relatada

No exemplo acima, o twin dispositivo contém um `batteryLevel` propriedade relatado pelo aplicativo do dispositivo. Essa propriedade possibilita consultar e operar em dispositivos de acordo com o último nível de bateria relatadas. Outro exemplo teria os recursos de dispositivo de relatório de aplicativo de dispositivo ou opções de conectividade.

Observe como relatadas propriedades simplificar cenários onde back-end está interessado no último valor conhecido de uma propriedade. Mensagens de [dispositivo à nuvem] [ lnk-d2c] se back-end precisa processar telemetria de dispositivo no formulário de sequências de eventos de marca, como série temporal.

### <a name="desired-configuration-example"></a>Exemplo de configuração desejada

No exemplo acima, o `telemetryConfig` propriedades desejadas e relatadas são usadas pelo aplicativo do back-end e o dispositivo para sincronizar a configuração de telemetria para esse dispositivo. Por exemplo:

1. O back-end do aplicativo define a propriedade desejada com o valor de configuração desejada. Aqui está a parte do documento com a propriedade desejada:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. O aplicativo do dispositivo será notificado da alteração imediatamente se conectado ou na primeira reconectar-se. O aplicativo de dispositivo reporta a configuração atualizada (ou uma condição de erro usando o `status` propriedade). Aqui está a parte de propriedades relatadas:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. O back-end do aplicativo pode manter controlar os resultados da operação de configuração em vários dispositivos, [consultando] [ lnk-query] gêmeos.

> [AZURE.NOTE] Os trechos acima são exemplos, otimizados para legibilidade, de uma maneira possível para codificar uma configuração de dispositivo e seu status. Hub de IoT não impõe um esquema específico para as propriedades desejadas e relatados em gêmeos o dispositivo.

Em muitos casos gêmeos são usados para sincronizar operações de execução demorada como atualizações de firmware. Consulte usar [propriedades desejadas para configurar dispositivos] [ lnk-twin-properties] para obter mais informações sobre como usar propriedades para sincronizar e controlar longo executando operações em dispositivos.

## <a name="back-end-operations"></a>Operações de back-end

Back-end funciona com o twin usando as seguintes operações atômicas, expostas por meio de HTTP:

1. **Recuperar twin pela identificação**. Esta operação retorna o conteúdo do documento do twin, incluindo marcas e desejado, relatados e propriedades do sistema.
2. **Atualizar parcialmente twin**. Essa operação permite back-end parcialmente atualizar o twin marcas ou propriedades desejadas. A atualização parcial é expresso como um documento JSON que adiciona ou atualiza qualquer propriedade mencionada. Propriedades definidas como `null` são removidos. Por exemplo, a seguir cria uma nova propriedade desejada com valor `{"newProperty": "newValue"}`, substituirá o valor existente da `existingProperty` com `"otherNewValue"`e remove completamente `otherOldProperty`. Nenhuma alteração ocorrer para outras propriedades desejadas existentes ou marcas:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Substituir as propriedades desejadas**. Essa operação permite back-end substituir todas as suas propriedades desejadas e substituir um novo documento JSON para completamente `properties/desired`.
4. **Substituir marcas**. Analogamente para substituir as propriedades desejadas, este operações permite back-end substituir todas as marcas existentes e substituir um novo documento JSON para completamente `tags`.

Todas as operações acima suportam [concorrência otimista] [ lnk-concurrency] e exige a permissão de **ServiceConnect** , conforme definido na [segurança] [ lnk-security] artigo.

Essas operações, além de back-end pode consultar os gêmeos usando um tipo SQL da [linguagem de consulta][lnk-query]e executar operações em grandes conjuntos de gêmeos usando [trabalhos][lnk-jobs].

## <a name="device-operations"></a>Operações de dispositivo

O aplicativo de dispositivo funciona com o twin usando as seguintes operações atômicas:

1. **Recuperar twin**. Esta operação retorna o conteúdo do documento do twin (incluindo marcas e desejada, relatados e propriedades do sistema) para o dispositivo conectado no momento.
2. **Parcialmente atualização relatados propriedades**. Essa operação permite a atualização parcial das propriedades relatadas do dispositivo conectado no momento. Isso usa o mesmo formato de atualização JSON como back-end opostas atualização parcial das propriedades desejadas.
3. **Observe desejado propriedades**. O dispositivo atualmente conectado pode optar por ser notificado sobre as atualizações para as propriedades desejadas assim que eles ocorrem. O dispositivo recebe o mesmo formulário de atualização (substituição parcial ou total) executado pelo back-end.

Todas as operações acima exigem a permissão de **DeviceConnect** , conforme definido na [segurança] [ lnk-security] artigo.

O [dispositivo do Azure IoT SDKs] [ lnk-sdks] facilitam a usar as operações acima de vários idiomas e plataformas. Obter mais informações sobre os detalhes de primitivos IoT do Hub para a sincronização de propriedades desejadas podem ser encontradas no [fluxo de reconexão do dispositivo][lnk-reconnection].

> [AZURE.NOTE] Atualmente, Gêmeos de dispositivo são acessíveis somente a partir de dispositivos que se conectam a IoT Hub usando o protocolo MQTT.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como controlar o acesso ao seu hub IoT.

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Marcas, propriedades desejadas e relatadas são objetos JSON com as seguintes restrições:

* Todas as chaves de objetos JSON são cadeias de caracteres UNICODE 128 caracteres diferencia maiusculas de minúsculas. Permitidos caracteres excluir caracteres de controle UNICODE (segmentos C0 e C1), e `'.'`, `' '`, e `'$'`.
* Todos os valores no objeto JSON podem ser dos seguintes tipos de JSON: booliano, número, cadeia de caracteres, objeto. Matrizes não são permitidas.

## <a name="twin-size"></a>Tamanho de Twin

Hub de IoT impõe um limite de tamanho de 8KB nos valores de `tags`, `properties/desired`, e `properties/reported`, excluindo elementos somente leitura.
O tamanho é calculado, contando todos os caracteres UNICODE de excluindo controlam caracteres (segmentos C0 e C1) e espaço `' '` quando ele for exibido fora uma constante de cadeia de caracteres.
Hub de IoT irá rejeitar com erro todas as operações que desejam aumentar o tamanho desses documentos acima do limite.

## <a name="twin-metadata"></a>Twin metadados

Hub de IoT mantém o carimbo de hora da última atualização para cada objeto JSON nas propriedades desejados e relatados. Os carimbos de hora estão em UTC e codificado no formato [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Essas informações são mantidas em cada nível (não apenas as folhas da estrutura JSON) para preservar as atualizações que removam chaves do objeto.

## <a name="optimistic-concurrency"></a>Concorrência otimista

Marcas, desejadas e relatadas propriedades todos oferecem suporte concorrência otimista.
Marcas têm um etag, de acordo com as [RFC7232], que representa a representação de JSON da marca. Você pode usar isso em operações de atualização condicional do back-end para garantir a consistência.

Propriedades desejadas e relatadas não têm etags, mas tem uma `$version` valor que é garantir que seja incremental. Analogamente para uma etag, a versão pode ser usada pela parte atualização como (um aplicativo de dispositivo para uma propriedade relatada) ou back-end para uma propriedade desejado para impor a consistência das atualizações.

Versões também são úteis quando um agente observando (como o aplicativo de dispositivo observando as propriedades desejadas) tem reconciliar corridas entre o resultado de uma operação de recuperar e uma notificação de atualização. Na seção [fluxo de reconexão do dispositivo] [ lnk-reconnection] fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo

Hub de IoT não preserva as notificações de atualização de propriedades desejadas para dispositivos desconectados. Ela segue que um dispositivo que está se conectando deve recuperar o documento completo propriedades desejadas, além de se inscrever para notificações de atualização. Considerando a possibilidade de corridas entre as notificações de atualização e recuperação completa, o seguinte fluxo deve ser verificado:

1. Aplicativo de dispositivo se conecta a um hub IoT.
2. Aplicativo de dispositivo se inscreve para propriedades desejadas as notificações de atualização.
3. Aplicativo de dispositivo recupera o documento completo para propriedades desejadas.

O aplicativo de dispositivo pode ignorar todas as notificações com `$version` menor ou igual a versão do documento recuperado completo. Isso é possível porque IoT Hub garante que sempre incrementam versões.

> [AZURE.NOTE] Essa lógica já é implementada no [dispositivo Azure IoT SDKs][lnk-sdks]. Essa descrição é útil somente se o aplicativo do dispositivo não é possível usar qualquer dispositivo Azure IoT SDKs e deve programar a interface MQTT diretamente.

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre Gêmeos de dispositivo, você pode estar interessado nos seguintes tópicos de guia desenvolvedor:

- [Invocar um método direto em um dispositivo][lnk-methods]
- [Agendar trabalhos em vários dispositivos][lnk-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado nos seguintes tutoriais do IoT Hub:

- [Como usar o twin de dispositivo][lnk-twin-tutorial]
- [Como usar twin propriedades][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png