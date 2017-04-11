<properties
   pageTitle="Gerenciar controladores de dispositivo de StorSimple | Microsoft Azure"
   description="Saiba como interromper, reiniciar, desligar ou redefinir seu controladores de dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Gerenciar seus controladores de dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve as operações diferentes que podem ser executadas em seus controladores de dispositivo StorSimple. Os controladores em seu dispositivo de StorSimple são controladores redundantes (ponto) em uma configuração ativo-passivo. Em um determinado momento, apenas um controlador está ativo e está processando todas as operações de disco e de rede. O outro controlador está em um modo passivo. Se o controlador ativo falhar, o controlador passivo fica ativo automaticamente.

Este tutorial inclui instruções passo a passo para gerenciar os controladores de dispositivo usando a:

- Seção de **controladores** da página de **manutenção** do serviço do Gerenciador de StorSimple
- Windows PowerShell para StorSimple.

Recomendamos que você gerencia os controladores de dispositivo por meio do serviço do Gerenciador de StorSimple. Se uma ação só pode ser executada usando o Windows PowerShell para StorSimple, o tutorial faz uma observação sobre ela.

Após ler este tutorial, você será capaz de:

- Reiniciar ou desligar um controlador de dispositivo de StorSimple
- Desligar um dispositivo StorSimple
- Redefina o dispositivo StorSimple padrões de fábrica


## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador

Controlador reinicialização ou desligamento não é necessário como parte da operação normal do sistema. Operações de desligamento para um controlador de dispositivo único são comuns somente em casos em que um componente de hardware do dispositivo com falha requer substituição. Também pode ser necessário reiniciar controlador em uma situação em que o desempenho seja afetado pelo uso de memória excessiva ou um controlador com mau funcionamento. Também precisam ser reiniciar um controlador após uma substituição de controlador bem-sucedida, se você deseja habilitar e testar o controlador substituído.

Reiniciar um dispositivo não é interrupções iniciadores conectados, considerando que o controlador passivo está disponível. Se um controlador passivo não está disponível ou virada desativado e reiniciar o controlador de ativo pode resultar na interrupção do serviço e o tempo de inatividade.

> [AZURE.IMPORTANT]

> - **Um controlador de execução nunca deverá ser removido física como isso resultaria em uma perda de redundância e aumentam o risco de tempo de inatividade.**

> - O procedimento a seguir se aplica somente ao dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar o dispositivo virtual, consulte [trabalhar com o dispositivo virtual](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Você pode reiniciar ou desligar um controlador de dispositivo único usando o portal de clássico Azure do serviço do Gerenciador de StorSimple ou o Windows PowerShell para StorSimple

Para gerenciar os controladores de dispositivo de portal clássico do Azure, execute as seguintes etapas.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Para reiniciar ou desligar um controlador no portal clássico

1. Navegue até **dispositivos > manutenção**.

1. Vá para o **Status de Hardware** e verifique se o status dos dois controladores em seu dispositivo está **Íntegro**.

    ![Verificar controladores de dispositivo de StorSimple são íntegros](./media/storsimple-manage-device-controller/IC766017.png)

1. Na parte inferior da página de **manutenção** , clique em **Gerenciar controladores**.

    ![Gerenciar controladores de dispositivo de StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Se você não conseguir ver **Gerenciar controladores**, você precisa instalar atualizações. Para obter mais informações, consulte [atualizar seu dispositivo StorSimple](storsimple-update-device.md).

1. Na caixa de diálogo **Alterar controlador de configurações** , faça o seguinte:
    1. Na lista suspensa **Selecionar controlador** , selecione o controlador que você deseja gerenciar. As opções são controlador 0 e 1 de controlador. Esses controladores também são identificados como ativo ou passivo.

        >[AZURE.NOTE] Um controlador não pode ser gerenciado se estiver indisponível ou virada desativado e ela não aparecerá na lista suspensa.

    2. Na lista suspensa **Selecionar ação** , escolha **reinicie controlador** ou **desligar controlador**.

        ![Reinicie o controlador de passivo de dispositivo de StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
    3. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-device-controller/IC740895.png).

Isto irá reiniciar ou desligar o controlador. A tabela abaixo resume os detalhes do que acontece dependendo as seleções feitas na caixa de diálogo **Configurações de controlador de alteração** .  


|Seleção #|Se você optar por …|Isso acontecerá.|
|---|---|---|
|1.|Reinicie o controlador passivo.|Um trabalho será criado para reiniciar o controlador e você será notificado depois o trabalho é criado com êxito. Isso iniciará a reinicialização do controlador. Você pode monitorar o processo de reinicialização acessando **serviço > Painel de controle > Exibir logs de operação** e filtrando por parâmetros específicos ao seu serviço.|
|2.|Reinicie o controlador de ativo.|Você verá o seguinte aviso: "Se você reiniciar o controlador de ativo, o dispositivo falhará ao longo do controlador de passivo. Você deseja continuar?" </br>Se você optar por continuar com esta operação, as próximas etapas serão idênticas às usadas para reiniciar o controlador de passivo (consulte seleção 1).|
|3.|Desligar o controlador de passivo.|Você verá a seguinte mensagem: "após desligamento estiver concluído, você precisará pressionar o botão de energia em seu controlador ativá-lo. Tem certeza de que deseja desligar este controlador?" </br>Se você optar por continuar com esta operação, as próximas etapas serão idênticas às usadas para reiniciar o controlador de passivo (consulte seleção 1).|
|4.|Desligar o controlador de ativo.|Você verá a seguinte mensagem: "após desligamento estiver concluído, você precisará pressionar o botão de energia em seu controlador ativá-lo. Tem certeza de que deseja desligar este controlador?" </br>Se você optar por continuar com esta operação, as próximas etapas serão idênticas às usadas para reiniciar o controlador de passivo (consulte seleção 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou desligar um controlador no Windows PowerShell para StorSimple
Execute as seguintes etapas para desligar ou reiniciar um único controlador em seu dispositivo de StorSimple do portal de clássico do Azure.


1. Acesse o dispositivo usando o console serial ou uma sessão de telnet de um computador remoto. Conecte-se ao controlador de 0 ou 1 de controlador seguindo as etapas no [Acabamento de uso para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. No menu console serial, escolha a opção 1, **fazer logon com acesso total**.

1. Na mensagem de faixa, anote o controlador que você está conectado à (controlador 0 ou 1 de controlador) e se ele está ativo ou o controlador (reserva) passivo.
    - Para desligar a um único controlador, no prompt, digite:

        `Stop-HcsController`

        Isso será encerrado o controlador que você está conectado. Se você parar o controlador de ativo, depois ele falhará ao longo do controlador de passivo antes ele termina.
    - Para reiniciar um controlador, no prompt, digite:

        `Restart-HcsController`

        Isso irá reiniciar o controlador que você está conectado. Se você reiniciar o controlador de ativo, ele falhará ao longo do controlador de passivo antes da reinicialização.


## <a name="shut-down-a-storsimple-device"></a>Desligar um dispositivo StorSimple

Esta seção explica como desligar um em execução ou um dispositivo de StorSimple falha de um computador remoto. Um dispositivo está desativado após desligar os dois controladores de dispositivo. Um desligamento de dispositivo é feito quando o dispositivo está sendo movido física ou é interpretado sair do serviço.

> [AZURE.IMPORTANT] Antes de desligar o dispositivo, verifique a integridade dos componentes do dispositivo. Navegue até **dispositivos > manutenção > Status de Hardware** e verifique se o status de todos os componentes do LED é verde. Apenas um dispositivo eficaz, terá um status verde. Se seu dispositivo está sendo desligado para substituir um componente não está funcionando corretamente, você verá um falha (vermelho) ou um status (amarelo) degradado para os respectivos componentes.

#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple

1. Use o procedimento [reiniciar ou desligar um controlador](#restart-or-shut-down-a-single-controller) para identificar e desligar o controlador passivo em seu dispositivo. Você pode executar essa operação no portal de clássico do Azure ou no Windows PowerShell para StorSimple.
2. Repita a etapa acima para desligar o controlador de ativo.
3. Agora, você precisará examinar o plano traseiro do dispositivo. Depois que os dois controladores são desligar completamente, o status LED nos dois controladores de deve ser piscando vermelho. Se você precisar desligar o dispositivo completamente neste momento, clique em Inverter os interruptores em energia e refrigeração módulos (PCMs) para a posição desativado. Isso deve desativar o dispositivo.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Redefinir o dispositivo para as configurações padrão de fábrica

> [AZURE.IMPORTANT] Se você precisar redefinir seu dispositivo para as configurações padrão de fábrica, contate o Microsoft Support. O procedimento descrito abaixo deve ser usado somente em conjunto com o Microsoft Support.

Este procedimento descreve como redefinir seu dispositivo Microsoft Azure StorSimple às configurações padrão de fábrica usando o Windows PowerShell para StorSimple.
Redefinir um dispositivo remove todos os dados e configurações de todo o cluster por padrão.

Execute as seguintes etapas para redefinir seu dispositivo Microsoft Azure StorSimple para as configurações padrão de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir o dispositivo para as configurações padrão no Windows PowerShell para StorSimple

1. Acesse o dispositivo por meio de seu console serial. Verifique a mensagem de faixa para garantir que você está conectado ao controlador de ativos.

1. No menu console serial, escolha a opção 1, **fazer logon com acesso total**.

1. No prompt, digite o seguinte comando para redefinir o cluster inteiro, removendo todas as configurações de dados, metadados e controlador:

    `Reset-HcsFactoryDefault`

    Para redefinir em vez disso, um único controlador, use o cmdlet [Redefinir HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) com a `-scope` parâmetro.)

    O sistema será reiniciado várias vezes. Você será notificado quando a redefinição foi concluída com êxito. Dependendo do modelo do sistema, pode levar 45 a 60 minutos para um dispositivo 8100 e 60-90 minutos para um 8600 concluir esse processo.

    > [AZURE.TIP]

    > - Se você estiver usando o atualização 1.2 ou anterior use o `–SkipFirmwareVersionCheck` parâmetro para ignorar a verificação de versão de firmware (caso contrário, você verá um erro de incompatibilidade de firmware: redefinição de fábrica não pode continuar devido a uma incompatibilidade nas versões firmware. ).

    > - O procedimento de redefinição de fábrica pode falhar para dispositivos StorSimple estiver executando Update 1 ou 1.1 no portal do governo e executaram substituto bem-sucedida controlador único ou duplo (com controladores de substituição que foram enviados com software de pré-atualização 1). Isso acontece quando a fábrica Redefinir imagem é validada a presença de um arquivo de SHA1 no controlador de que não existe para pré-atualização 1 o software. Se você vir que esta fábrica redefinir falha, contate o Microsoft Support para ajudá-lo com as próximas etapas. Esse problema não é visto com controladores de substituição que foram enviados de fábrica com o software posterior ou Update 1.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre o gerenciamento de controladores de dispositivo

Nesta seção, podemos ser resumido algumas das perguntas frequentes sobre o gerenciamento controladores de dispositivo de StorSimple.

**P.** O que acontece se os dois os controladores em meu dispositivo são íntegra e virada em e eu reiniciar ou desligar o controlador de ativo?

**R.** Se os dois os controladores em seu dispositivo íntegra e virada, você será solicitado para confirmação. Você pode optar por:

- **Reinicie o controlador de ativo** – você será notificado de que a reinicialização um controlador active causará o dispositivo para alternar para o controlador de passivo. O controlador irá reiniciar.

- **Desligar um controlador de ativo** – você será notificado de que a desligar um controlador active resultará em tempo de inatividade. Você também precisa pressionar o botão de energia no dispositivo para ativar o controlador.

**P.** O que acontece se o controlador de passivo em meu dispositivo não está disponível ou virada desativado e eu reiniciar ou desligar o controlador de ativo?

**R.** Se o controlador de passivo em seu dispositivo está indisponível ou virada desativado e você optar por:

- **Reinicie o controlador de ativo** – você será notificado de que continuar a operação resultará em uma interrupção temporária do serviço, e você será solicitado para confirmação.

- **Desligar um controlador de ativo** – você será notificado de que a continuar a operação resultará em tempo de inatividade e que você precisará pressionar o botão de energia em um ou ambos os controladores para ativar o dispositivo. Você será solicitado para confirmação.

**P.** Quando faz a reinicialização do controlador ou desligamento Falha ao progresso?

**R.** Reiniciar ou desligar um controlador pode falhar se:

- Uma atualização de dispositivo está em andamento.

- Reiniciar controlador já está em andamento.

- Um desligamento controlador já está em andamento.

**P.** Como você descobrir se um controlador foi reiniciado ou desligado?

**R.** Você pode verificar o status do controlador na página manutenção. O status do controlador indicará se um controlador foi reiniciado ou desligado. Além disso, a página alertas conterá um alerta informativo se o controlador foi reiniciado ou desligado. As operações de reinicialização e desligamento do controlador também são registradas em logs de operação. Para obter mais informações sobre logs de operação, vá para [Exibir os logs de operação](storsimple-service-dashboard.md#view-the-operations-logs).

**P.** Há algum impacto para os saídas como resultado de failover do controlador?

**R.** As conexões de TCP entre iniciadores e controlador de ativo serão redefinidas como resultado de failover do controlador, mas serão possível restabelecer quando o controlador passivo assume a operação. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de e/s entre iniciadores e o dispositivo durante o curso desta operação.

**P.** Como posso devolver meu controlador de serviço depois que ele foi encerrado e removido?

**R.** Para retornar um controlador de serviço, insira-o no gabinete conforme descrito em [Substituir um módulo de controlador em seu dispositivo de StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Próximas etapas

- Se você encontrar problemas com sua controladores de dispositivo de StorSimple que você não consegue resolver usando os procedimentos listados neste tutorial, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md).

- Para saber mais sobre como usar o serviço do Gerenciador de StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
