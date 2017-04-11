<properties
 pageTitle="Visão geral de gerenciamento de dispositivo do IoT Hub | Microsoft Azure"
 description="Este artigo fornece uma visão geral do gerenciamento de dispositivo no Hub do Azure IoT: ciclo de vida do enterprise dispositivo, reinicie, fábrica redefinir, atualização de firmware, configuração, Gêmeos de dispositivo, consultas, trabalhos"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Visão geral do gerenciamento de dispositivo do Azure IoT Hub (prévia)

## <a name="introduction"></a>Introdução

Hub de IoT Azure fornece os recursos e um modelo de extensibilidade que habilitam o dispositivo e desenvolvedores de back-end para criar soluções de gerenciamento de dispositivo IoT robustas. IoT dispositivos variam de sensores restritas e microcontroladores única finalidade, gateways poderosos que rotear comunicações para grupos de dispositivos.  Além disso, os casos de uso e os requisitos para operadores IoT variarem significativamente setores.  Apesar desta variação, gerenciamento de dispositivo do Azure IoT Hub fornece os recursos, padrões e bibliotecas de código para atender a um conjunto diverso de dispositivos e usuários finais.

Uma parte fundamental da criação de uma empresa bem-sucedida IoT solução para fornecer uma estratégia como operadores lidar com o gerenciamento contínuo do seu conjunto de dispositivos. Operadores de IoT exigem ferramentas simples e confiáveis e aplicativos que habilitá-las para focalizar os aspectos mais estratégicos de seus trabalhos. Este artigo fornece:

- Uma breve visão geral de abordagem Azure IoT Hub para gerenciamento de dispositivo.
- Uma descrição do Princípios comuns de gerenciamento de dispositivo.
- Uma descrição do ciclo de vida do dispositivo.
- Uma visão geral de padrões de gerenciamento de dispositivo comuns.

## <a name="iot-device-management-principles"></a>Princípios de gerenciamento de dispositivo IoT

IoT traz um conjunto exclusivo de desafios de gerenciamento de dispositivo e cada solução corporativa deve abordar os princípios a seguir:

![Gráfico de princípios gerenciamento do Azure IoT Hub dispositivo][img-dm_principles]

- **Escala e automação**: IoT soluções exigem ferramentas simples que podem automatizar tarefas de rotina e habilitar uma equipe de operações relativamente pequeno gerenciar milhões de dispositivos. Diariamente, operadores esperam lidar com operações de dispositivo remotamente, em massa e somente a ser alertado quando surgirem problemas que exigem sua atenção direta.

- **Abertura e compatibilidade**: IoT o ecossistema de dispositivo é muito diverso. Ferramentas de gerenciamento devem ser adaptadas para acomodar uma infinidade de classes de dispositivo, plataformas e protocolos. Operadores devem ser capazes de oferecer suporte a vários tipos de dispositivos, da mais limitados incorporados processo único chips para computadores poderosos e totalmente funcionais.

- **Reconhecimento de contexto**: IoT ambientes são dinâmicos e constante mudança. Confiabilidade do serviço é fundamental. Operações de gerenciamento de dispositivo devem fator em janelas de manutenção, estados de rede e energia, condições em uso e localização geográfica do dispositivo para garantir que tempo de inatividade de manutenção não afetam as operações de negócios essenciais ou criar condições perigosas SLA.

- **Muitas funções de serviço**: suporte a fluxos de trabalho exclusivos e processos IoT funções de operações é fundamental. A equipe de operações deve trabalhar harmoniously com as restrições de determinado internos departamentos de TI.  Eles também devem encontrar maneiras sustentáveis informações de operações de dispositivo de superfície em tempo real para supervisores e outras funções de gerente de negócios.

## <a name="iot-device-lifecycle"></a>Ciclo de vida do IoT dispositivo

Há um conjunto de estágios do gerenciamento de dispositivo gerais que são comuns a todos os projetos de IoT empresariais. Em IoT do Azure, existem cinco estágios no ciclo de vida de dispositivo IoT:

![As cinco fases de ciclo de vida de dispositivo do Azure IoT: planejar, provisionar, configurar, monitorar, retirar][img-device_lifecycle]

Em cada um desses cinco estágios, há vários requisitos de operador de dispositivo que devem ser atendidos para fornecer uma solução completa:

- **Planejar**: habilitar operadores criar um esquema de metadados do dispositivo que permita facilmente com precisão consultar e um grupo de dispositivos para operações de gerenciamento de massa de destino. Você pode usar o twin dispositivo para armazenar metadados dispositivo na forma de marcas e propriedades.

    *Leituras adicionais*: [Introdução ao Gêmeos de dispositivo][lnk-twins-getstarted], [Gêmeos de dispositivo de compreender][lnk-twins-devguide], [como usar twin propriedades][lnk-twin-properties]

- **Provisionar**: provisionar novos dispositivos IoT hub com segurança e habilitar operadores de descobrir imediatamente os recursos do dispositivo.  Usar o registro do dispositivo de IoT Hub para criar as credenciais e identidades de dispositivo flexível e executar essa operação em massa usando um trabalho. Construa dispositivos para relatar seus recursos e condições por meio de propriedades de dispositivo no twin do dispositivo.

    *Leituras adicionais*: [Gerenciar identidades do dispositivo][lnk-identity-registry], [gerenciamento de massa de identidades do dispositivo][lnk-bulk-identity], [como usar twin propriedades][lnk-twin-properties]

- **Configurar**: facilitar alterações de configuração em massa e as atualizações de firmware para dispositivos enquanto mantém a integridade e a segurança. Execute essas operações de gerenciamento de dispositivo em massa usando propriedades desejadas ou com métodos diretos e trabalhos de transmissão.

    *Leituras adicionais*: [usar métodos diretos][lnk-c2d-methods], [chamar um método direto em um dispositivo][lnk-methods-devguide], [como usar propriedades de twin][lnk-twin-properties], [agenda e trabalhos de transmissão][lnk-jobs], [Agendar trabalhos em vários dispositivos][lnk-jobs-devguide]

- **Monitor**: monitorar integridade de conjunto de dispositivo geral, o status das operações em andamento, e operadores para problemas que podem exigir sua atenção o alerta.  Aplica o twin dispositivo para permitir que dispositivos condições operacionais do relatório em tempo real e o status de operações de atualização. Crie relatórios de painel poderosos que expor os problemas mais imediatos usando dispositivo twin consultas.

    *Leituras adicionais*: [como usar propriedades de twin][lnk-twin-properties], [linguagem de consulta para gêmeos e trabalhos][lnk-query-language]

- **Desativar**: substituir ou encerrar dispositivos após uma falha, atualização ciclo, ou no final da vida útil do serviço.  Use o twin dispositivo para manter informações de dispositivo se o dispositivo físico está sendo substituído ou arquivadas se sendo desativado. Use o registro de dispositivo IoT Hub para revogar com segurança identidades de dispositivo e as credenciais.

    *Leituras adicionais*: [como usar propriedades de twin][lnk-twin-properties], [Gerenciar identidades de dispositivos][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Padrões de gerenciamento de dispositivo do IoT Hub

Hub de IoT habilita o seguinte conjunto de padrões de gerenciamento de dispositivo.  Os [tutoriais de gerenciamento de dispositivo] [ lnk-get-started] mostram mais detalhadamente como estender esses padrões para ajustar seu cenário exato e como criar novos padrões com base nesses modelos de core.

- **Reinicie** - o aplicativo de back-end informa o dispositivo por meio de um método direto que ele iniciou uma reinicialização.  O dispositivo usa o dispositivo twin relatados propriedades para atualizar o status de reinicialização do dispositivo.

    ![Gerenciamento de dispositivo do Azure IoT Hub reinicializar gráfico padrão][img-reboot_pattern]

- **Redefinir a fábrica** - o aplicativo de back-end informa o dispositivo por meio de um método direto que ele iniciou uma redefinição de fábrica.  O dispositivo usa o twin dispositivo propriedades relatadas para atualizar a fábrica Redefinir status do dispositivo.

    ![Azure fábrica de gerenciamento de dispositivo de IoT Hub redefinir gráfico padrão][img-facreset_pattern]

- **Configuração** - o aplicativo de back-end usa as propriedades de twin desejado do dispositivo para configurar o software em execução no dispositivo.  O dispositivo usa o dispositivo twin relatados propriedades atualizar status de configuração do dispositivo.

    ![Gráfico de padrão de configuração de gerenciamento do IoT Hub dispositivo Azure][img-config_pattern]

- **Atualização do firmware** - o aplicativo de back-end informa o dispositivo por meio de um método direto que ele iniciou uma atualização de firmware.  O dispositivo inicia um processo de várias etapas para baixar o pacote de firmware, aplicar o pacote de firmware e finalmente se reconectar ao serviço IoT Hub.  Durante o processo de etapa de mult, o dispositivo usa o dispositivo twin relatados propriedades atualizar o andamento e o status do dispositivo.

    ![Firmware de gerenciamento de dispositivo do Azure IoT Hub atualizar gráfico padrão][img-fwupdate_pattern]

- **Relatar o andamento e o status** - back-end do aplicativo executa consultas de twin de dispositivo, em um conjunto de dispositivos, para gerar um relatório sobre o status e o progresso das ações executados nos dispositivos.

    ![Gerenciamento de dispositivo do Azure IoT Hub relatar o andamento e o status gráfico padrão][img-report_progress_pattern]

## <a name="next-steps"></a>Próximas etapas

Você pode usar os recursos, padrões e bibliotecas de código que o gerenciamento de dispositivos do Azure IoT Hub fornece, para criar aplicativos IoT que atendem os requisitos de operador de IoT corporativos dentro em cada estágio de ciclo de vida do dispositivo.

Para continuar a aprender sobre os recursos de gerenciamento de dispositivo do Azure IoT Hub, consulte a [Introdução ao gerenciamento de dispositivo do Azure IoT Hub] [ lnk-get-started] tutorial.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md