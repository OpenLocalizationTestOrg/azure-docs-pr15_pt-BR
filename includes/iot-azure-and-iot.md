
# <a name="azure-and-internet-of-things"></a>Windows Azure e Internet das coisas

Bem-vindo ao Microsoft Azure e a Internet das coisas (IoT). Este artigo apresenta uma arquitetura da solução IoT que descreve as características comuns de uma solução de IoT, que você poderá implantar usando os serviços do Azure. Soluções de IoT exigem seguro, comunicação bidirecional entre dispositivos, possivelmente numeração milhões e um back-end da solução. Por exemplo, um back-end da solução pode usar automatizado, previsão de análise para descobrir ideias de seu fluxo de evento de dispositivo a nuvem.

Hub do Azure IoT é um bloco de construção chave quando você implementa essa arquitetura de solução IoT usando os serviços do Azure. IoT Suite fornece implementações completas e de ponta a ponta dessa arquitetura para cenários de IoT específicos. Por exemplo: 

- A solução de *monitoramento remoto* permite monitorar o status dos dispositivos, como máquinas de vendas automáticas. 
- A solução de *manutenção de previsão* ajuda a prever as necessidades de manutenção de dispositivos como pumps em estações bombeamento remotas e para evitar o tempo de inatividade não programado.

## <a name="iot-solution-architecture"></a>Arquitetura da solução IoT

O diagrama a seguir mostra uma arquitetura de solução IoT típica. O diagrama não inclui os nomes de quaisquer serviços específicos do Azure, mas descreve os elementos-chave em uma arquitetura de solução IoT genérico. Nesta arquitetura, dispositivos IoT coletam dados que enviam para um gateway de nuvem. O gateway de nuvem disponibiliza os dados para processamento por outros serviços de back-end de onde os dados são entregues para outros aplicativos de linha de negócios ou operadores humanos por meio de um painel ou outro dispositivo de apresentação.

![Arquitetura da solução IoT][img-solution-architecture]

> [AZURE.NOTE] Para obter uma discussão detalhada sobre arquitetura IoT, consulte a [Arquitetura de referência do Microsoft Azure IoT][lnk-refarch].

### <a name="device-connectivity"></a>Conectividade do dispositivo

Nesta arquitetura de solução IoT, dispositivos enviar telemetria, como leituras de sensor estação de bombeamento, para um ponto de extremidade de nuvem para processamento e de armazenamento. Em um cenário de previsão de manutenção, back-end pode usar o fluxo de dados para determinar quando uma bomba específica requer manutenção. Dispositivos também podem receber e responder aos comandos de nuvem para dispositivo lendo mensagens a partir de um ponto de extremidade de nuvem. Por exemplo, no cenário com manutenção de previsão de indisponibilidade do back-end da solução pode enviar comandos ao outros pumps na estação de bombeamento para começar a fluxos de redirecionamento antes de manutenção está prestes a começar a certificar-se de que o engenheiro de manutenção pode começar quando ela é recebida.

Um dos maiores desafios enfrentados IoT projetos é como conectar dispositivos de forma segura e confiável para o back-end da solução. Dispositivos de IoT possuem características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. IoT dispositivos:

- Costumam ser incorporados sistemas com nenhum operador humano.
- Pode ser implantada em locais remotos, onde o acesso físico é caro.
- Pode ser alcançados apenas por meio do back-end da solução. Não há outros interagir com o dispositivo.
- Podem ter limitado de energia e recursos de processamento.
- Talvez seja necessário conectividade de rede intermitente, lenta ou cara.
- Talvez seja necessário usar protocolos de aplicativo proprietário, personalizado ou específicos do setor.
- Podem ser criados usando um grande conjunto de plataformas de hardware e software populares.

Além dos requisitos acima, qualquer solução IoT também deve fornecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante é rígido e demorado para implementar usando tecnologias tradicionais como contêineres web e os agentes de mensagens. Hub do Azure IoT e os SDKs do dispositivo IoT facilitam a implementação de soluções que atendem a esses requisitos.

Um dispositivo pode se comunicar diretamente com um ponto de extremidade do gateway de nuvem, ou se o dispositivo não é possível usar qualquer um dos protocolos communications que suporta o gateway de nuvem, ele pode se conectar através de um gateway intermediário. Por exemplo, o [gateway de protocolo do Azure IoT] [ lnk-protocol-gateway] pode executar a conversão de protocolo se dispositivos não é possível usar qualquer um dos protocolos que ofereça suporte a IoT Hub.

### <a name="data-processing-and-analytics"></a>Processamento de dados e análise

Na nuvem, um IoT solução back-end é onde ocorre a maioria do processamento de dados, como filtragem e agregação de telemetria e roteá-lo a outros serviços. A solução IoT back-end:

- Recebe telemetria em escala de seus dispositivos e determina como processar e armazenar os dados. 
- Talvez permitem enviar comandos da nuvem para o dispositivo específico.
- Fornece recursos de registro do dispositivo que permitem aos dispositivos de provisão e para controlar quais dispositivos são permitidos para se conectar à sua infra-estrutura.
- Permite controlar o estado dos seus dispositivos e monitorar suas atividades.

No cenário de previsão de manutenção, o back-end da solução armazena dados de telemetria histórica. Back-end pode usar esses dados utilizado para identificar padrões que indicam a manutenção é devida de uma bomba específica.

Soluções de IoT podem incluir loops de comentários automáticos. Por exemplo, um módulo de análise no back-end pode identificar da telemetria a temperatura de um dispositivo específico está acima níveis operacionais normais. Solução poderá enviar um comando para o dispositivo, instruindo-la para executar uma ação corretiva.

### <a name="presentation-and-business-connectivity"></a>Apresentação e corporativos de conectividade

A camada de conectividade de negócios e apresentação permite que os usuários finais interagir com a solução IoT e os dispositivos. Permite que os usuários exibam e analisar os dados coletados a partir de seus dispositivos. Esses modos de exibição podem ter o formato dos painéis ou relatórios de BI que podem exibir ambos os dados de histórico ou perto de dados em tempo real. Por exemplo, um operador pode verificar o status da estação de bombeamento específico e ver todos os alertas gerados pelo sistema. Essa camada também permite que a integração do IoT solução back-end com aplicativos de linha de negócios existentes unir em processos corporativos ou fluxos de trabalho. Por exemplo, a solução de manutenção de previsão pode integrar com um sistema de agendamento que manuais online um engenheiro para visitar a uma estação de bombeamento quando a solução identifica uma bomba que precisam de manutenção.

![Painel de solução IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
