<properties
    pageTitle="Personalizando soluções pré-configuradas | Microsoft Azure"
    description="Fornece orientação sobre como personalizar as soluções do Azure IoT pacote pré-configurado."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personalizar uma solução pré-configurada

As soluções pré-configuradas fornecidas com o pacote do Azure IoT demonstram os serviços dentro do pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Desse ponto inicial, há vários de locais nos quais você pode estender e personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## <a name="finding-the-source-code"></a>Localizando o código-fonte

O código-fonte para as soluções pré-configuradas está disponível no GitHub nos seguintes repositórios:

- Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Manutenção de previsão: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

O código-fonte para as soluções pré-configuradas é fornecido para demonstrar padrões e práticas usadas para implementar a funcionalidade de ponta a ponta de uma solução de IoT usando Azure IoT Suite. Você pode encontrar mais informações sobre como criar e implantar as soluções nos repositórios GitHub.

## <a name="changing-the-preconfigured-rules"></a>Alterando as regras pré-configuradas

A solução de monitoramento remota inclui três trabalhos de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar lógica de regras, telemetria e informações do dispositivo exibida para a solução.

Os trabalhos de análise de fluxo de três e sua sintaxe é descrito em detalhes o [monitoramento explicação passo a passo de solução pré-configurada remoto](iot-suite-remote-monitoring-sample-walkthrough.md). 

Você pode editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica a seu cenário. Você pode encontrar os trabalhos de análise de fluxo da seguinte maneira:
 
1. Acesse o [portal do Azure](https://portal.azure.com).
2. Navegue até o grupo de recursos com o mesmo nome de sua solução IoT. 
3. Selecione o trabalho de análise de fluxo de Azure que você deseja modificar. 
4. Pare o trabalho selecionando **Parar**no conjunto de comandos. 
5. Edite as entradas, consulta e saídas.

    É uma modificação simples para alterar a consulta para o trabalho de **regras** usar um **"<"** em vez de um **">"**. O portal de solução ainda mostrará **">"** quando você editar uma regra, mas você observará o comportamento é invertido devido a alteração no trabalho subjacente.

6. Iniciar o trabalho

> [AZURE.NOTE] Painel de monitoramento remoto depende dados específicos, portanto alterar os trabalhos pode causar painel falha.

## <a name="adding-your-own-rules"></a>Adicionando suas próprias regras

Além de alterar os trabalhos de análise de fluxo de Azure pré-configurado, você pode usar o portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## <a name="customizing-devices"></a>Personalização de dispositivos

Uma das atividades mais comuns extensão está trabalhando com dispositivos específicos para seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterem um dispositivo simulado para corresponder ao seu cenário, ou usando o [SDK do dispositivo IoT][] conectar o dispositivo físico à solução.

Para um guia passo a passo para a adição de dispositivos à solução pré-configurado monitoramento remota, consulte [Iot Suite conectando dispositivos](iot-suite-connecting-devices.md) e a [Amostra de SDK C monitoramento remoto](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) que foi projetado para funcionar com a solução de pré-configurado monitoramento remota.

### <a name="creating-your-own-simulated-device"></a>Criar seu próprio simulado dispositivo

Incluído no remoto monitoramento solução código-fonte (mencionado acima), é um simulador .NET. Este simulator é aquele provisionados como parte da solução e pode ser alterado para enviar metadados diferentes, Telemetria ou responder a comandos diferentes.

O simulator pré-configurado na solução pré-configurado monitoramento remoto é um dispositivo mais fria que emite telemetria temperatura e umidade, você pode modificar o simulator no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando você tiver bifurcada repositório GitHub.

### <a name="available-locations-for-simulated-devices"></a>Locais disponíveis para dispositivos simulados

O conjunto padrão de locais está em Seattle/Redmond, Washington, Estados Unidos. Você pode alterar esses locais no [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Criando e usando seu próprio dispositivo (físico)

O [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas de conexão vários tipos de dispositivo (idiomas e sistemas operacionais) em IoT soluções.

## <a name="modifying-dashboard-limits"></a>Modificando limites de painel

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos exibidos no menu suspenso do dashboard

O padrão é 200. Você pode alterar esse número em [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de pins para exibir no controle de mapa do Bing

O padrão é 200. Você pode alterar esse número em [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período de tempo do gráfico de telemetria

O padrão é 10 minutos. Você pode alterar isso em [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurar manualmente as funções de aplicativo

O procedimento a seguir descreve como adicionar funções de aplicativo de **Administração** e **somente leitura** a uma solução pré-configurada. Observe que soluções pré-configuradas provisionadas do site azureiotsuite.com já incluem as funções de **administrador** e **somente leitura** .

Membros da função **somente leitura** podem ver o painel de controle e a lista de dispositivos, mas não tem permissão para adicionar dispositivos, alterar os atributos de dispositivo ou enviar comandos.  Membros da função **administrador** têm acesso completo a todas as funcionalidades na solução.

1. Acesse o [portal clássico Azure][lnk-classic-portal].

2. Selecione o **Active Directory**.

3. Clique no nome do locatário AAD usada quando você provisionado sua solução.

4. Clique em **aplicativos**.

5. Clique no nome do aplicativo que corresponde ao seu nome de solução pré-configurada. Se você não vir seu aplicativo na lista, selecione **aplicativos minha empresa possui** em **Mostrar** lista suspensa e clique na marca de seleção.

6.  Na parte inferior da página, clique em **Gerenciar manifesto** e, em seguida, **Baixe manifesto**.

7. Isso baixa um arquivo de .json em sua máquina local.  Abra este arquivo para edição em um editor de texto de sua escolha.

8. Na terceira linha do arquivo .json, você encontrará:

  ```
  "appRoles" : [],
  ```
  Substitua isso com o seguinte:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Salve o arquivo atualizado .json (você pode substituir o arquivo existente).

10.  No Portal de gerenciamento do Azure, na parte inferior da página, selecione **Gerenciar manifesto** e **Carregar manifesto** para carregar o arquivo de .json que você salvou na etapa anterior.

11. Agora que você adicionou as funções de **administrador** e **somente leitura** para o seu aplicativo.

12. Para atribuir uma dessas funções para um usuário no seu diretório, consulte [permissões no site azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Comentários

Você tem uma personalização que você gostaria de ver coberto neste documento? Adicione as sugestões de recurso a [Voz do usuário](https://feedback.azure.com/forums/321918-azure-iot)ou comentário sobre este artigo abaixo. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções para personalizar as soluções pré-configurado, consulte:

- [Conectar o aplicativo de lógica à sua solução Azure IoT Suite monitoramento remoto pré-configurado][lnk-logicapp]
- [Usar telemetria dinâmica com a solução pré-configurada monitoramento remota][lnk-dynamic]
- [Metadados de informações de dispositivo no monitoramento remoto pré-configurado solução][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[Dispositivo IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
