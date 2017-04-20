> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

Este passo a passo da [nuvem de dispositivo simulado carregar amostra] mostra como usar o [SDK do Windows Azure IoT Gateway] [ lnk-sdk] enviar telemetria do dispositivo para nuvem para IoT Hub da simulado dispositivos.

Este passo a passo aborda:

1. **Arquitetura**: as informações de arquiteturais importantes sobre a amostra simulados carregar de nuvem do dispositivo.

2. **Executar e compilação**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

O exemplo de nuvem de dispositivo simulado carregar mostra como usar o SDK para criar um gateway que envia telemetria de dispositivos simulados para um hub IoT. Os dispositivos simulados não podem conectar diretamente ao IoT Hub porque:

- Os dispositivos não use um protocolo de comunicação compreendido por IoT Hub.
- Os dispositivos não são inteligentes lembrar a identidade atribuída a eles por IoT Hub.

O gateway resolve esses problemas para os dispositivos simulados das seguintes maneiras:

- O gateway entende o protocolo usado pelos dispositivos simulados, recebe o dispositivo para nuvem telemetria entre os dispositivos e encaminha essas mensagens para o IoT Hub usando um protocolo compreendido pelo hub.
- O gateway armazena as identidades do IoT Hub em nome dos dispositivos simulados e atua como um proxy quando os dispositivos simulados enviar mensagens para o IoT Hub.

O diagrama a seguir mostra os principais componentes da amostra, incluindo os módulos do gateway:

![][1]


> [AZURE.NOTE] Os módulos não passar mensagens diretamente entre si. Os módulos publicar mensagens em um corretor interno que entrega as mensagens para os outros módulos usando um mecanismo de inscrição, conforme mostrado no diagrama a seguir. Para obter mais informações, consulte [Introdução ao SDK do Gateway IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>Módulo de inclusão de protocolo

Este módulo é o ponto de partida para obtenção de dados de dispositivos, através do gateway e em nuvem. No exemplo, o módulo executa quatro tarefas:

1.  Ele cria os dados de temperatura simulado.
    
    Observação: se você estava usando dispositivos reais, o módulo seria ler dados desses dispositivos físicos.

2.  Ele coloca os dados de temperatura simulado no conteúdo de uma mensagem.

3.  Ele adiciona uma propriedade com um endereço MAC de falsa à mensagem que contém os dados de temperatura simulado.

4.  Ele disponibiliza a mensagem para o próximo módulo na cadeia.

> [AZURE.NOTE] O módulo chamado **inclusão de protocolo X** no diagrama acima é chamado **simuladas dispositivo** no código-fonte.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; módulo IoT Hub ID

Este módulo examina de mensagens que incluem uma propriedade que contém o endereço MAC, adicionado pelo módulo de inclusão de protocolo do dispositivo simulado. Se o módulo encontrar essa propriedade, adiciona outra propriedade com uma chave de dispositivo do IoT Hub à mensagem e, em seguida, disponibiliza a mensagem para o próximo módulo na cadeia. Isso é como o exemplo associa as identidades de dispositivo do IoT Hub com dispositivos simulados. O desenvolvedor configura o mapeamento entre endereços MAC e identidades do IoT Hub manualmente como parte da configuração do módulo. 

> [AZURE.NOTE]  Este exemplo usa um endereço MAC como um identificador exclusivo do dispositivo e correlaciona-la com uma identidade do dispositivo IoT Hub. No entanto, você pode escrever seu próprio módulo que usa um identificador exclusivo diferente. Por exemplo, você pode ter dispositivos com dados de telemetria que tem um nome exclusivo do dispositivo incorporado que você poderia usar para determinar a identidade do dispositivo IoT Hub ou de números de série exclusivos.

### <a name="iot-hub-communication-module"></a>Módulo de comunicação IoT Hub

Este módulo leva mensagens com um IoT Hub dispositivo identidade atribuída pelo módulo anterior e envia o conteúdo da mensagem para o IoT Hub usando HTTP. O HTTP é um dos três protocolos compreendidos por IoT Hub.

Em vez de abrir uma conexão para o IoT Hub para cada dispositivo simulado, este módulo abre uma conexão de HTTP único do gateway ao hub IoT e multiplexes conexões de todos os dispositivos simulados por essa conexão. Isso permite que um único gateway conectar muitos mais dispositivos, simulados ou caso contrário, que seria possível se ele aberto uma conexão exclusiva para cada dispositivo.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Amostra de carregamento de nuvem do dispositivo simulada]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md