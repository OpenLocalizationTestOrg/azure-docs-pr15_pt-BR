<properties
    pageTitle="Introdução ao soluções pré-configuradas | Microsoft Azure"
    description="Siga este tutorial para saber como implantar uma solução do Azure IoT pacote pré-configurado."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Começar a usar as soluções pré-configuradas

## <a name="introduction"></a>Introdução

Azure IoT Suite [soluções pré-configuradas] [ lnk-preconfigured-solutions] combinar vários serviços IoT do Azure para oferecer soluções de ponta a ponta que implementam cenários de negócios IoT comuns. A solução de *monitoramento remoto* pré-configurado se conecta ao e monitora seus dispositivos. Você pode usar a solução para analisar o fluxo de dados de seus dispositivos e para aprimorar os resultados comerciais fazendo processos responder automaticamente a esse fluxo de dados.

Este tutorial mostra como provisionar a solução pré-configurada monitoramento remota. Ele também orienta os recursos básicos de solução de monitoração remota. Você pode acessar muitos desses recursos por meio do painel de solução que implanta junto com a solução pré-configurada:

![Painel de solução pré-configurada monitoramento remoto][img-dashboard]

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [AZURE.NOTE]  Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Exibir o painel de solução

No painel de solução permite que você gerencie a solução implantada. Por exemplo, você pode exibir telemetria, adicionar dispositivos e configurar regras.

1.  Quando o provisionamento é concluído e bloco de sua solução pré-configurada indica **pronto**, clique em **Iniciar** para abrir seu portal de solução de monitoramento remoto em uma nova guia.

    ![Inicie a solução pré-configurado][img-launch-solution]

2.  Por padrão, o portal de solução mostra o *Painel de solução*. Você pode selecionar outros modos de exibição usando o menu à esquerda.

    ![Painel de solução pré-configurada monitoramento remoto][img-dashboard]

O painel exibe as seguintes informações:

- O mapa exibe a localização de cada dispositivo conectado à solução. Quando você executa a solução, há quatro dispositivos simulados. Os dispositivos simulados são implementados como WebJobs do Azure e a solução usa API do Bing Maps para plotar informações no mapa.
- Painel **Histórico de telemetria** plota telemetria umidade e temperatura em um dispositivo selecionado no próximo exibe agregar dados como média, mínimo e máximo umidade e em tempo real.
- Painel **Histórico de alarme** mostra eventos de alarme recentes quando um valor de telemetria excedeu um limite. Você pode definir seus próprios alarmes além dos exemplos criados pela solução pré-configurado.

## <a name="view-the-device-list"></a>Exibir a lista de dispositivo

Lista de dispositivos mostra todos os dispositivos registrados na solução. Você exibe e editar metadados de dispositivo, adicione ou remova dispositivos e envia comandos para dispositivos.

1.  Clique em **dispositivos** no menu da esquerda para mostrar a *lista de dispositivos* para esta solução.

    ![Lista de dispositivos no painel de controle][img-devicelist]

2.  Lista de dispositivos mostra que há quatro dispositivos simulados criados pelo processo de provisionamento.

3.  Clique em um dispositivo na lista de dispositivos para exibir os detalhes do dispositivo.

    ![No painel de detalhes do dispositivo][img-devicedetails]

O painel de **Detalhes do dispositivo** contém três seções:

- A seção de **ações** lista as ações que você pode executar no dispositivo. Se você desabilitar o dispositivo, já não é permitido enviar Telemetria ou receber comandos. Se você desativar um dispositivo, você pode, em seguida, habilitá-lo. Você pode adicionar uma regra associada ao dispositivo que aciona um alarme quando um valor de telemetria excede um limite. Você também pode enviar um comando para um dispositivo. Quando um dispositivo se conecta pela primeira vez, ele informa a solução os comandos que ele pode responder.
- A seção de **Propriedades do dispositivo** lista os metadados do dispositivo. Alguns dos metadados vem do próprio dispositivo (como o fabricante) e alguns é gerado pela solução (como a hora de criação). Você pode editar os metadados de dispositivo a partir daqui.
- A seção de **Chaves de autenticação** lista as teclas que o dispositivo pode usar para autenticar com a solução.

## <a name="send-a-command-to-a-device"></a>Enviar um comando para um dispositivo

O painel de detalhes do dispositivo mostra todos os comandos que dá suporte a um dispositivo específico e permite que você envie comandos para um dispositivo. Quando um dispositivo é iniciado, ele envia informações sobre os comandos que ele dá suporte à solução.

1.  Clique em **comandos** no painel de detalhes do dispositivo para o dispositivo selecionado.

    ![Comandos de dispositivo no painel de controle][img-devicecommands]

2.  Selecione **PingDevice** na lista de comando.

3.  Clique em **Enviar comando**.

4.  Você pode ver o status do comando no histórico do comando.

    ![Status de comando no painel de controle][img-pingcommand]

A solução rastreia o status de cada comando envia. Inicialmente, o resultado é **pendente**. Quando o dispositivo reporta que ele tenha executado o comando, o resultado é definido para o **sucesso**.

## <a name="add-a-new-simulated-device"></a>Adicionar um novo dispositivo simulado

Quando você implanta a solução pré-configurado, você automaticamente provisionar os quatro dispositivos de exemplo, que você pode ver na lista de dispositivos. Estas são *dispositivos simulados* executado em um WebJob do Azure. Dispositivos simulados tornam mais fácil para você experimentar a solução pré-configurada sem a necessidade de implantar dispositivos reais, físicos. Se você quiser conectar um dispositivo real à solução, consulte a [solução pré-configurado de monitoramento de conectar seu dispositivo para o computador remoto] [ lnk-connect-rm] tutorial.

As etapas a seguir mostram como adicionar um dispositivo simulado à solução:

1.  Navegue de volta para a lista de dispositivos.

2.  Clique em **+ para adicionar um dispositivo** no canto inferior esquerdo para adicionar um dispositivo.

    ![Adicionar um dispositivo à solução pré-configurado][img-adddevice]

3.  Clique em **Adicionar novo** no bloco **Dispositivo simulada** .

    ![Definir novos detalhes de dispositivo no dashboard][img-addnew]
    
    Além de criar um novo dispositivo simulado, você também pode adicionar um dispositivo físico se você optar por criar um **Dispositivo personalizado**. Para saber mais sobre como conectar dispositivos físicos à solução, consulte [conectar seu dispositivo para o pacote de IoT monitoramento solução pré-configurada remoto][lnk-connect-rm].

4.  Selecione **Deixe-me definir minha ID de dispositivo**e digite um nome de identificação de dispositivo exclusivo como **mydevice_01**.

5.  Clique em **criar**.

    ![Salvar um novo dispositivo][img-definedevice]

6. Na etapa 3 de **Adicionar um dispositivo simulado**, clique em **Concluir** para retornar à lista de dispositivos.

7. Você pode exibir o seu dispositivo **executando** na lista de dispositivos.

    ![Novo dispositivo de modo de exibição em lista de dispositivo][img-runningnew]

8. Você também pode exibir a telemetria simulada de seu novo dispositivo no painel:

    ![Modo de exibição de telemetria do novo dispositivo][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Editar os metadados de dispositivo

Quando um dispositivo primeiro se conecta à solução, ele envia seus metadados para a solução. Quando você edita os metadados de dispositivo por meio do painel de solução, ele envia os novos valores de metadados para o dispositivo e armazena os novos valores do banco de dados de DocumentDB de solução. Para obter mais informações, consulte o [registro de identidade do dispositivo e DocumentDB][lnk-devicemetadata].

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione seu novo dispositivo na **Lista de dispositivos**e clique em **Editar** para editar as **Propriedades do dispositivo**:

    ![Editar metadados de dispositivo][img-editdevice]

3. Role para baixo e faça uma alteração as vales latitude e longitude. Clique em **Salvar alterações no registro do dispositivo**.

    ![Editar metadados de dispositivo][img-editdevice2]

4. Navegue de volta para o painel de controle, o local do dispositivo foi alterado no mapa:

    ![Editar metadados de dispositivo][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Adicionar uma regra para o novo dispositivo

Existem regras para o novo dispositivo que você acabou de adicionar. Nesta seção, você adiciona uma regra que aciona um alarme quando a temperatura relatada pelo novo dispositivo excede 47 graus. Antes de começar, observe que o histórico de telemetria para o novo dispositivo no painel mostra que a temperatura de dispositivo nunca excede 45 graus.

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione seu novo dispositivo na **Lista de dispositivos**e, em seguida, clique em **Adicionar regra** para adicionar uma regra para o dispositivo.

3. Crie uma regra que usa **temperatura** como o campo de dados e usa **AlarmTemp** como a saída quando a temperatura excede 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule]

4. Clique em **Salvar e exibir regras** para salvar suas alterações.

5.  Clique em **comandos** no painel de detalhes do dispositivo para o novo dispositivo.

    ![Adicionar uma regra de dispositivo][img-adddevicerule2]

6.  Selecione **ChangeSetPointTemp** na lista de comando e defina **SetPointTemp** 45. Clique em **Enviar comando**:

    ![Adicionar uma regra de dispositivo][img-adddevicerule3]

7.  Navegue de volta para o painel de solução. Após alguns instantes, você verá uma nova entrada no painel **Histórico de alarme** quando a temperatura relatada pelo seu novo dispositivo excede o limite de 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule4]

8. Você pode revisar e editar todas as regras na página **regras** do painel de controle:

    ![Regras de dispositivo de lista][img-rules]

9. Você pode revisar e editar todas as ações que podem ser executadas em resposta a uma regra na página **ações** do painel de controle:

    ![Ações de dispositivo de lista][img-actions]

> [AZURE.NOTE] É possível definir ações que podem enviar uma mensagem de email ou SMS em resposta a uma regra ou integrar com um sistema de linha de negócios por meio de um [Aplicativo de lógica][lnk-logic-apps]. Para obter mais informações, consulte o [aplicativo de lógica se conectar ao seu Azure IoT Suite monitoramento remoto pré-configurado solução][lnk-logicapptutorial].

## <a name="other-features"></a>Outros recursos

Usando o portal de solução, que você pode pesquisar para dispositivos com características específicas, como um número de modelo:

![Pesquisa para um dispositivo][img-search]

Você pode desativar um dispositivo e depois que ela é desabilitada você pode removê-lo:

![Desativar e remover um dispositivo][img-disable]

## <a name="behind-the-scenes"></a>Nos bastidores

Quando você implantar uma solução pré-configurada, o processo de implantação cria vários recursos na assinatura do Azure selecionada. Você pode exibir esses recursos no [portal]do Azure[lnk-portal]. O processo de implantação cria um **grupo de recursos** com um nome com base no nome que você escolhe para sua solução pré-configurado:

![Solução pré-configurada no portal do Azure][img-portal]

Você pode exibir as configurações de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte para a solução pré-configurado. O código de fonte de solução pré-configurada monitoramento remoto está no [azure-iot-monitoramento remoto] [ lnk-rmgithub] GitHub repositório:

- A pasta **DeviceAdministration** contém o código-fonte do painel.
- A pasta **Simulator** contém o código-fonte do dispositivo simulado.
- A pasta **EventProcessor** contém o código-fonte para o processo de back-end que trata a entrada telemetria.

Quando terminar, você pode excluir a solução pré-configurada da sua assinatura do Azure na [azureiotsuite.com] [ lnk-azureiotsuite] site. Este site permite que você exclua facilmente todos os recursos que foram provisionados quando você criou a solução pré-configurado.

> [AZURE.NOTE] Para garantir que você exclua tudo relacionados à solução pré-configurado, excluí-la a [azureiotsuite.com] [ lnk-azureiotsuite] do site e não simplesmente exclui o grupo de recursos no portal.

## <a name="next-steps"></a>Próximas etapas

Agora que você já implantou uma solução de trabalho pré-configurado, você pode continuar a introdução ao Suite IoT lendo os seguintes artigos:

- [Monitoramento remoto pré-configurado solução explicação passo a passo][lnk-rm-walkthrough]
- [Conectar o dispositivo à solução pré-configurado monitoramento remota][lnk-connect-rm]
- [Permissões no site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
