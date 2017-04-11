<properties
 pageTitle="Guia do desenvolvedor - registro de identidade de dispositivo | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - descrição do registro de identidade do dispositivo e como usá-lo para gerenciar seus dispositivos"
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

# <a name="manage-device-identities-in-iot-hub"></a>Gerenciar identidades de dispositivo no IoT Hub

## <a name="overview"></a>Visão geral

Cada hub IoT tem um registro de identidade do dispositivo que armazena informações sobre os dispositivos que são permitidos para se conectar ao hub. Antes de um dispositivo pode se conectar a um hub, deve haver uma entrada para esse dispositivo no registro de identidade de dispositivo do hub. Um dispositivo também deve autenticar com o hub com base em credenciais armazenadas no registro de identidade do dispositivo.

Em um alto nível, o registro do dispositivo de identidade é uma coleção restante capaz de recursos de identidade do dispositivo. Quando você adiciona uma entrada no registro, o Hub de IoT cria um conjunto de recursos de cada dispositivo no serviço como fila que contém mensagens de nuvem para dispositivo em trânsito.

### <a name="when-to-use"></a>Quando usar

Use o registro de identidade do dispositivo quando você precisar provisionar dispositivos que se conectam a você IoT hub e quando você precisar controlar o acesso de cada dispositivo para os pontos de extremidade voltados para o dispositivo no seu hub.

> [AZURE.NOTE] O registro do dispositivo de identidade não contém qualquer metadado específico do aplicativo.

## <a name="device-identity-registry-operations"></a>Operações de registro do dispositivo identidade

Registro de identidade de dispositivo do IoT Hub expõe as seguintes operações:

* Criar identidade de dispositivo
* Atualizar identidade de dispositivo
* Recuperar dispositivo identidade por ID
* Excluir identidade de dispositivo
* Até 1000 identidades de lista
* Exportar todas as identidades ao Azure blob storage
* Importar identidades do armazenamento de blob do Microsoft Azure

Todas essas operações podem usar concorrência otimista, como especificado em [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] A única maneira de recuperar todas as identidades no registro de identidade de um hub é usar a [Exportar] [ lnk-export] funcionalidade.

Um registro de identidade do IoT Hub dispositivo:

- Não contém qualquer metadados do aplicativo.
- Pode ser acessado como um dicionário, usando o **deviceId** como chave.
- Não dá suporte a consultas significativa.

Uma solução IoT normalmente tem um armazenamento de solução específicas separado que contém metadados específicos do aplicativo. Por exemplo, o armazenamento de solução específicas em uma solução de construção inteligente seria gravar a sala na qual um sensor de temperatura é implantado.

> [AZURE.IMPORTANT] Use somente o registro do dispositivo de identidade para operações de provisionamento e gerenciamento de dispositivo. Operações de alta produtividade em tempo de execução não devem depender executar operações no registro de identidade do dispositivo. Por exemplo, verificar o estado de conexão de um dispositivo antes de enviar um comando não é um padrão com suporte. Verifique o [otimização taxas] [ lnk-quotas] para o registro do dispositivo de identidade e a [pulsação de dispositivo] [ lnk-guidance-heartbeat] padrão.

## <a name="disable-devices"></a>Desativar dispositivos

Você pode desativar dispositivos atualizando a propriedade de **status** de uma identidade no registro. Normalmente, você deve usar essa propriedade em dois cenários:

- Durante um processo de coordenação provisionamento. Para obter mais informações, consulte [Provisionamento de dispositivo][lnk-guidance-provisioning].
- Se, por algum motivo, você pode considerar um dispositivo seja comprometido ou tornou não autorizado.

## <a name="import-and-export-device-identities"></a>Importar e exportar identidades de dispositivo

Você pode exportar identidades de dispositivos em massa do registro de identidade do hub um IoT, usando operações assíncronas no [ponto de extremidade de provedor de recurso IoT Hub][lnk-endpoints]. Exportações são trabalhos de execução longa que usa um contêiner de blob fornecido pelo cliente e salvar dados de identidade de dispositivo ler a partir do registro de identidade.

Você pode importar identidades do dispositivo em massa registro de identidade do hub um IoT, usando operações assíncronas no [ponto de extremidade de provedor de recurso IoT Hub][lnk-endpoints]. Importações são trabalhos de execução longa que usam dados em um contêiner de blob fornecido pelo cliente e gravar dados de identidade de dispositivo no registro de identidade do dispositivo.

- Para obter informações detalhadas sobre a importação e exportação APIs, consulte o [provedor de recursos do IoT Hub APIs REST][lnk-resource-provider-apis].
- Para saber mais sobre como executar importação e exportação trabalhos, consulte [gerenciamento de massa de identidades de dispositivo do IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Provisionamento de dispositivo

Os dados do dispositivo que armazena uma determinada solução IoT dependem de requisitos específicos da solução. Mas, como mínimo, uma solução deve armazenar identidades do dispositivo e chaves de autenticação. Hub de IoT Azure inclui um registro de identidade que pode armazenar valores para cada dispositivo como IDs, chaves de autenticação e códigos de status. Uma solução pode usar outros serviços Azure como armazenamento de tabela do Microsoft Azure, armazenamento de blob do Microsoft Azure ou DocumentDB do Azure para armazenar quaisquer dados de dispositivo adicionais.

*Provisionamento de dispositivo* é o processo de adicionar os dados do dispositivo inicial para os armazenamentos em sua solução. Para permitir que um novo dispositivo para se conectar ao seu hub, você deve adicionar uma nova ID do dispositivo e chaves para o registro de identidade IoT Hub. Como parte do processo de provisionamento, você precisará inicializar dados específicos do dispositivo em outros armazenamentos de solução.

## <a name="device-heartbeat"></a>Pulsação de dispositivo

O registro de identidade IoT Hub contém um campo chamado **connectionState**. Você só deve usar o campo **connectionState** durante o desenvolvimento e depuração, IoT soluções devem não consultar o campo tempo de execução (por exemplo, para verificar se um dispositivo estiver conectado para decidir se deseja enviar uma mensagem de nuvem para dispositivo ou um SMS).

Se sua solução IoT precisa saber se um dispositivo estiver conectado (em tempo de execução ou com mais precisão que fornece a propriedade **connectionState** ), sua solução deve implementar o *padrão de pulsação*.

No padrão de pulsação, o dispositivo envia mensagens de dispositivo à nuvem pelo menos uma vez cada valor fixo de tempo (por exemplo, pelo menos uma vez a cada hora). Isso significa que mesmo se um dispositivo não tem quaisquer dados para enviar, ele ainda envia uma mensagem de dispositivo à nuvem vazia (normalmente com uma propriedade que identifica como uma pulsação). No lado do serviço, a solução mantém um mapa com a última pulsação recebida para cada dispositivo e pressupõe que haja um problema com um dispositivo se ele não receber uma mensagem de pulsação dentro do tempo esperado.

Uma implementação mais complexa pode incluir as informações de [monitoramento de operações] [ lnk-devguide-opmon] para identificar dispositivos que estão tentando se conectar ou comunicar mas falhando. Ao implementar o padrão de pulsação, certifique-se de verificar [IoT Hub cotas e limitações][lnk-quotas].

> [AZURE.NOTE] Se o estado de conexão do dispositivo unicamente para determinar se deve enviar mensagens de nuvem para dispositivo precisa de uma solução IoT e mensagens não são transmita para grandes conjuntos de dispositivos, um padrão mais simples a serem consideradas é usar um tempo de expiração curto. Isso atinge o mesmo resultado como manter um registro de estado de conexão do dispositivo usando o padrão de pulsação, enquanto estiver sendo significativamente mais eficiente. Também é possível, solicitando confirmações de mensagem, para ser notificado por IoT Hub de quais dispositivos são capazes de receber mensagens e que não estão online ou são falhou.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre o registro de identidade devcie.

## <a name="device-identity-properties"></a>Propriedades de identidade de dispositivo

Identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades.

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| deviceId | obrigatória, somente leitura atualizações | Uma cadeia de caracteres diferencia maiusculas de minúsculas (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obrigatória, somente leitura | Uma cadeia de caracteres diferencia maiusculas de minúsculas, gerados pelo hub até 128 caracteres. Isso é usado para distinguir os dispositivos com o mesmo **deviceId**, quando eles foram excluídos e recriados. |
| ETag | obrigatória, somente leitura | Uma cadeia de caracteres que representa um etag fraco para a identidade do dispositivo, acordo com as [RFC7232][lnk-rfc7232].|
| AUTH | opcional | Um objeto composto contendo materiais de segurança e informações de autenticação. |
| AUTH.symkey | opcional | Um objeto composto contendo um principal e uma tecla secundária, armazenados no formato base64. |
| status | Necessário | Um indicador de acesso. Pode ser **habilitado** ou **desabilitado**. Se **ativado**, o dispositivo tem permissão para se conectar. Se **desabilitado**, esse dispositivo não poderá acessar qualquer ponto de extremidade voltados para o dispositivo. |
| statusReason | opcional | Uma cadeia de caracteres longas 128 que armazena o motivo para o status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime | somente leitura | Um indicador temporal, mostrando a data e hora da última atualização de status. |
| connectionState | somente leitura | Um campo que indica o status de conexão: **conectado** ou **desconectado**. Este campo representa o modo de exibição de IoT Hub do status de conexão do dispositivo. **Importante**: esse campo deve ser usado somente para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente para dispositivos usando MQTT ou AMQP. Além disso, ele é baseado no nível de protocolo ping (ping MQTT ou AMQP ping), e ele pode ter um atraso máximo de apenas 5 minutos. Por essas razões, pode haver falsos positivos, como dispositivos relatada como conectado, mas que realmente estiver desconectado. |
| connectionStateUpdatedTime | somente leitura | Um indicador de temporal, mostrando a data e a última vez que o estado da conexão foi atualizado. |
| lastActivityTime  | somente leitura | Um indicador de temporal, mostrando a data e a última vez o dispositivo conectado, recebido ou enviado uma mensagem. |

> [AZURE.NOTE] Estado de Conexão só pode representar o modo de exibição de IoT Hub do status da conexão. Atualizações para esse estado podem ser atrasadas, dependendo das configurações e condições da rede.

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o registro de identidade de dispositivo do IoT Hub, você pode estar interessado nos seguintes tópicos de guia desenvolvedor:

- [Controlar o acesso às IoT Hub][lnk-devguide-security]
- [Usar Gêmeos de dispositivo para sincronizar o estado e configurações][lnk-devguide-device-twins]
- [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado no tutorial IoT Hub seguinte:

- [Introdução ao Azure IoT Hub][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md