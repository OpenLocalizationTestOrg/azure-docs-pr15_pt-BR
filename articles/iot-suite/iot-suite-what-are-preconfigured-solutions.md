<properties
 pageTitle="Azure IoT soluções pré-configuradas | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado soluções e sua arquitetura com links para recursos adicionais."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quais são as soluções do Azure IoT pacote pré-configurado?

As soluções do Azure IoT pacote pré-configurado são implementações de padrões de solução IoT comuns que você pode implantar no Azure usando sua assinatura. Você pode usar as soluções predefinidas:

- Como ponto de partida para seus próprios soluções IoT.
- Para saber mais sobre padrões comuns nos IoT solução design e desenvolvimento.

Cada solução pré-configurada é uma implementação completa de ponta a ponta que usa dispositivos simulados para gerar telemetria.

Além de implantação e executando as soluções no Azure, você pode baixar o código fonte completo e, em seguida, personalizar e estender a solução para atender às suas necessidades específicas de IoT.

> [AZURE.NOTE] Para implantar uma das soluções pré-configuradas, visite [Microsoft Azure IoT Suite][lnk-azureiotsuite]. O artigo [Introdução com as soluções IoT pré-configurado] [ lnk-getstarted-preconfigured] fornece mais informações sobre como implantar e executar uma das soluções.

A tabela a seguir mostra como as soluções mapeiam para recursos específicos do IoT:

| Solução | Inclusão de dados | Identidade do dispositivo | Comando e controle | Regras e ações | Análise de previsão |
|------------------------|-----|-----|-----|-----|-----|
| [Monitoramento remoto][lnk-getstarted-preconfigured] | Sim | Sim | Sim | Sim | -   |
| [Manutenção de previsão][lnk-predictive-maintenance] | Sim | Sim | Sim | Sim | Sim |

- *Inclusão de dados*: entrada de dados em escala para a nuvem.
- *Dispositivo identidade*: gerenciar identidades exclusivas de cada dispositivo conectado.
- *Comando e controle*: enviar mensagens para um dispositivo da nuvem para fazer com que o dispositivo para executar alguma ação.
- *Regras e ações*: back-end solução usa regras para agir em dados específicos do dispositivo na nuvem.
- *Análise de previsão*: aplica de back-end solução analisa dados de dispositivo à nuvem para prever quando ações específicas devem ocorrer. Por exemplo, analisando telemetria do mecanismo de aviões para determinar quando a manutenção do mecanismo é devida.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Visão geral da solução remoto monitoramento pré-configurado

Escolhemos discutir a solução pré-configurada monitoramento remota neste artigo porque ele ilustra vários elementos de design comuns que compartilham as outras soluções.

O diagrama a seguir ilustra os principais elementos da solução monitoramento remoto. As seções a seguir fornecem mais informações sobre esses elementos.

![Arquitetura de solução de monitoramento pré-configurado remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Quando você implanta a solução de pré-configurado monitoramento remota, quatro dispositivos simulados são previamente provisionados na solução que simule um dispositivo de refrigeração. Esses dispositivos simulados têm um modelo interno de temperatura e umidade que emite telemetria. Esses dispositivos simulados estão incluídos para ilustrar o fluxo de ponta a ponta de dados por meio da solução e para fornecer uma fonte conveniente de telemetria e um destino para comandos se você for um desenvolvedor de back-end usando a solução como ponto de partida para uma implementação personalizada.

Quando um dispositivo primeiro se conecta ao IoT Hub na solução pré-configurado monitoramento remota, a mensagem de informações do dispositivo enviada ao hub IoT enumera a lista de comandos que o dispositivo pode responder. Na solução pré-configurado monitoramento remota, os comandos são: 

- *Ping dispositivo*: O dispositivo responde a esse comando com uma confirmação. Isso é útil para verificar que o dispositivo está ainda ativo e ouvinte.
- *Iniciar telemetria*: instrui o dispositivo para começar a enviar telemetria.
- *Parar de telemetria*: instrui o dispositivo para interromper o envio de telemetria.
- *Alterar ponto definido temperatura*: controla os valores de telemetria temperatura simulada envia o dispositivo. Isso é útil para testar a lógica de back-end.
- *Diagnóstico de telemetria*: controla se o dispositivo deve enviar a temperatura externa como telemetria.
- *Alterar dispositivo estado*.: define a propriedade de metadados de estado de dispositivo que informa o dispositivo. Isso é útil para testar a lógica de back-end.

Você pode adicionar mais dispositivos simulados à solução que emitir a mesma telemetria e responder aos mesmos comandos. 

## <a name="iot-hub"></a>Hub de IoT

Nesta solução pré-configurado, a instância do IoT Hub corresponde ao *Gateway de nuvem* em uma típica [arquitetura de solução de IoT][lnk-what-is-azure-iot].

Um hub IoT recebe telemetria de dispositivos a um único ponto de extremidade. Um hub IoT também mantém os pontos de extremidade específicos dispositivo onde cada dispositivos podem recuperar os comandos que são enviados para ele.

O hub de IoT disponibiliza a telemetria recebida através da telemetria no lado do serviço ler o ponto de extremidade.

## <a name="azure-stream-analytics"></a>Análise de fluxo Azure

A solução pré-configurado usa três [Azure fluxo Analytics] [ lnk-asa] trabalhos (ASA) para filtrar o fluxo de telemetria dos dispositivos:


- *Trabalho de DeviceInfo* - saídas dados a um hub de evento que direcione dispositivo registro mensagens específicas, enviadas quando um dispositivo se conecta ou em resposta a um comando de **alterar o estado do dispositivo** , para o registro do dispositivo de solução (um banco de dados do DocumentDB). 
- *Trabalho de telemetria* - envia todos telemetria bruta ao Azure blob storage para armazenamento de resfriado e calcula as agregações de telemetria exibidas no painel de solução.
- *Trabalho de regras* - filtra o fluxo de telemetria para valores que excede os limites de regra e produz os dados a um hub de evento. Quando uma regra é acionado, o modo de exibição do painel de portal de solução exibe este evento como uma nova linha na tabela de histórico de alarme e aciona uma ação com base nas configurações definidas nos modos de exibição regras e ações no portal de solução.

Nesta solução pré-configurado, os trabalhos ASA fazem parte à **solução IoT back-end** em uma típica [arquitetura de solução de IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Processador de evento

Nesta solução pré-configurado, o processador de evento formulários parte da **solução IoT back-end** em uma típica [arquitetura de solução de IoT][lnk-what-is-azure-iot].

Os trabalhos **DeviceInfo** e **regras** ASA enviar sua saída para hubs de evento para entrega de outros serviços de back-end. A solução usa um [EventPocessorHost] [ lnk-event-processor] instância, executado em um [WebJob][lnk-web-job], para ler as mensagens destas hubs de evento. O **EventProcessorHost** usa os dados de **DeviceInfo** para atualizar os dados de dispositivo no banco de dados DocumentDB e usa os dados de **regras** para chamar o aplicativo de lógica e os alertas exibem no portal de solução de atualização.

## <a name="device-identity-registry-and-documentdb"></a>Registro de identidade do dispositivo e DocumentDB

Cada hub IoT inclui um [registro de identidade do dispositivo] [ lnk-identity-registry] que armazena as chaves de dispositivo. Hub de IoT usa essas informações autenticar dispositivos - um dispositivo deve ser registrado e ter uma chave válida antes que possa se conectar ao hub.

Essa solução armazena informações adicionais sobre dispositivos como seu estado, os comandos que oferecem suporte e outros metadados. A solução usa um banco de dados de DocumentDB para armazenar dados neste dispositivo solução específicos e o portal de solução recupera dados deste banco de dados de DocumentDB para exibição e edição.

A solução também deve manter as informações no registro de identidade do dispositivo sincronizado com o conteúdo do banco de dados DocumentDB. O **EventProcessorHost** usa os dados do trabalho de análise de fluxo de **DeviceInfo** para gerenciar a sincronização.

## <a name="solution-portal"></a>Portal de solução

![Painel de solução][img-dashboard]

O portal de solução é uma interface de usuário baseada na web que é implantado na nuvem como parte da solução pré-configurado. Ele permite que você:

- Exibir histórico de telemetria e alarme em um painel.
- Provisionar novos dispositivos.
- Gerenciar e monitorar dispositivos.
- Envie comandos para dispositivos específicos.
- Gerencie regras e ações.

Nesta solução pré-configurado, o portal de solução formulários parte de **solução de IoT back-end** e parte do **processamento e corporativos de conectividade** na típica [arquitetura de solução de IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre arquiteturas de solução IoT, consulte [os serviços do Microsoft Azure IoT: arquitetura de referência][lnk-refarch].

Agora você sabe que uma solução pré-configurada for, você pode começar por implantação da solução de *monitoramento remoto* pré-configurado: [começar a usar as soluções pré-configuradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md