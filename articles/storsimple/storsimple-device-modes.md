<properties 
   pageTitle="Alterar o modo de dispositivo StorSimple | Microsoft Azure"
   description="Descreve os modos de dispositivo StorSimple e explica como usar o Windows PowerShell para StorSimple para alterar o modo de dispositivo."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Alterar o modo de dispositivo em seu dispositivo de StorSimple

Este artigo fornece uma breve descrição dos vários modos em que o seu dispositivo StorSimple pode operar. Seu dispositivo de StorSimple pode funcionar em três modos: normal, manutenção e recuperação. 

Após ler este artigo, você saberá:

- Quais os modos de dispositivo de StorSimple são
- Como descobrir qual modo o dispositivo StorSimple está em
- Como mudar de normal para o modo de manutenção e *vice-versa*


As tarefas de gerenciamento acima só podem ser executadas por meio da interface do Windows PowerShell do seu dispositivo de StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre modos de dispositivo de StorSimple

Seu dispositivo de StorSimple pode operar no modo normal, manutenção ou recuperação. Cada um desses modos brevemente é descrita abaixo.

### <a name="normal-mode"></a>Modo normal

Isso é definido como o modo operacional normal para um dispositivo StorSimple totalmente configurado. Por padrão, o seu dispositivo deve estar no modo normal.

### <a name="maintenance-mode"></a>Modo de manutenção

Às vezes, o dispositivo StorSimple talvez precise ser colocados em modo de manutenção. Este modo permite executar a manutenção do dispositivo e instalar atualizações interrupções, como aqueles relacionados ao firmware do disco.

Você pode colocar o sistema em modo de manutenção apenas através do Windows PowerShell para StorSimple. Todas as solicitações de i são pausadas nesse modo. Serviços como memória de acesso aleatório não-voláteis (NVRAM) ou o serviço de agrupamento também são interrompido. Os dois controladores de são reiniciados ao entrar ou sair desse modo. Quando você sair do modo de manutenção, todos os serviços continuarão e devem ser íntegros. Isso pode levar alguns minutos.

>[AZURE.NOTE]Modo de manutenção de **tem suporte apenas em um dispositivo está funcionando corretamente. Não é suportado em um dispositivo em que um ou ambos os controladores não estão funcionando.**
</br>

### <a name="recovery-mode"></a>Modo de recuperação

Modo de recuperação pode ser descrito como "Modo seguro do Windows com suporte de rede". Modo de recuperação envolve a equipe do Microsoft Support e permite executar o diagnóstico no sistema. O objetivo principal do modo de recuperação é recuperar os logs de sistema.

Se seu sistema entra no modo de recuperação, você deve contatar o Microsoft Support para as próximas etapas. Para obter mais informações, vá para o [Suporte da Microsoft de contato](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **Você pode colocar o dispositivo no modo de recuperação. Se o dispositivo está em um estado inválido, o modo de recuperação tenta obter o dispositivo em um estado em que o pessoal do Microsoft Support examiná-la.**

## <a name="determine-storsimple-device-mode"></a>Determinar o modo de dispositivo de StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo de dispositivo atual

1. Fazer logon no console serial do dispositivo seguindo as etapas no [Acabamento de uso para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Examine a mensagem de faixa no menu console serial do dispositivo. Esta mensagem explicitamente indica se o dispositivo está no modo de manutenção ou recuperação. Se a mensagem não contiver nenhuma informação específica referentes ao modo de sistema, o dispositivo está no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo StorSimple 

Você pode colocar o dispositivo StorSimple em modo de manutenção (a partir do modo normal) para executar a manutenção ou instalar atualizações de modo de manutenção. Execute os procedimentos a seguir para entrar ou sair do modo de manutenção.

> [AZURE.IMPORTANT] Antes de entrar no modo de manutenção, verifique se os dois controladores de dispositivo eficaz, acessando o **Status de Hardware** na página **manutenção** no portal de clássico do Azure. Se um ou ambos os controladores não estão íntegros, contate o Microsoft Support para as próximas etapas. Para obter mais informações, vá para o [Suporte da Microsoft de contato](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção

1. Fazer logon no console serial do dispositivo seguindo as etapas no [Acabamento de uso para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. No menu console serial, escolha a opção 1, **fazer logon com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é: `Password1`.

3. No prompt de comando, digite 

    `Enter-HcsMaintenanceMode`

4. Você verá uma mensagem de aviso informando que o modo de manutenção será interromper todas as solicitações de i e a conexão portal do Azure clássico de servidor, e você será solicitado para confirmação. Digite **Y** para entrar no modo de manutenção.

5. Ambos os controladores irá reiniciar. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está no modo de manutenção.


#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção

1. Faça logon no console serial do dispositivo. Verifique a partir da mensagem de faixa que seu dispositivo está no modo de manutenção.

2. No prompt de comando, digite:

    `Exit-HcsMaintenanceMode`

3. Uma mensagem de aviso e uma mensagem de confirmação serão exibida. Digite **Y** para sair do modo de manutenção.

4. Ambos os controladores irá reiniciar. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está no modo normal.


## <a name="next-steps"></a>Próximas etapas

Saiba como [Aplicar atualizações de modo normal e manutenção](storsimple-update-device.md) em seu dispositivo de StorSimple.

