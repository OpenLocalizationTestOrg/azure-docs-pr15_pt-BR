<properties
   pageTitle="Atualizar o dispositivo StorSimple | Microsoft Azure"
   description="Explica como usar o recurso de atualização StorSimple para instalar hotfixes e atualizações de modo normal e a manutenção."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Atualizar o dispositivo de série de 8000 StorSimple

## <a name="overview"></a>Visão geral

Os recursos de atualizações StorSimple permitem que você facilmente manter seu dispositivo StorSimple atualizados. Dependendo do tipo de atualização, você pode aplicar atualizações ao dispositivo através do Azure portal clássico ou por meio da interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Você pode aplicar dois tipos de atualizações de dispositivo: 

- Regulares (ou modo Normal) atualizações
- Atualizações de modo de manutenção

Você pode instalar atualizações regulares através do Azure portal clássico ou o Windows PowerShell; No entanto, você deve usar o Windows PowerShell para instalar as atualizações de modo de manutenção. 

Cada tipo de atualização é descrito separadamente, abaixo.

### <a name="regular-updates"></a>Atualizações regulares

Atualizações regulares são interrupções atualizações que podem ser instaladas quando o dispositivo está no modo Normal. Essas atualizações são aplicadas através do site Microsoft Update para cada controlador de dispositivo. 

> [AZURE.IMPORTANT] Um failover de controlador pode ocorrer durante o processo de atualização. No entanto, isso não afetará a disponibilidade do sistema ou a operação.

- Para obter detalhes sobre como instalar atualizações regulares através do portal de clássico Azure, consulte [instalar atualizações regulares através do Azure portal(#install-regular-updates-via-the-azure-classic-portal) clássico.

- Você também pode instalar atualizações regulares através do Windows PowerShell para StorSimple. Para obter detalhes, consulte [instalar atualizações regulares por meio do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações de modo de manutenção

As atualizações de modo de manutenção são interrupções atualizações como atualizações de firmware de disco. Essas atualizações exigem o dispositivo para ser colocados em modo de manutenção. Para obter detalhes, consulte [etapa 2: modo de manutenção insira](#step2). Você não pode usar o portal de clássico Azure para instalar as atualizações de modo de manutenção. Em vez disso, você deve usar o Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações de modo de manutenção, consulte [manutenção instalar atualizações de modo por meio do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Atualizações de modo de manutenção devem ser aplicadas separadamente para cada controlador. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar atualizações regulares através do portal de clássico Azure

Você pode usar o portal de clássico Azure para aplicar atualizações ao seu dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações regulares através do Windows PowerShell para StorSimple

Como alternativa, você pode usar o Windows PowerShell para StorSimple para aplicar atualizações regulares (modo Normal).

> [AZURE.IMPORTANT] Embora você possa instalar atualizações regulares usando o Windows PowerShell para StorSimple, é altamente recomendável que você instale atualizações regulares através do portal de clássico Azure. Começando com Update 1, pré-verificações de serão executadas antes de instalar as atualizações a partir do portal. Essas pré-verificações serão antecipado falhas e garantir uma experiência mais suave. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações do modo de manutenção por meio do Windows PowerShell para StorSimple

Você usa o Windows PowerShell para StorSimple para aplicar atualizações de modo de manutenção para o seu dispositivo StorSimple. Todas as solicitações de i são pausadas nesse modo. Serviços como memória de acesso aleatório não-voláteis (NVRAM) ou o serviço de agrupamento também são interrompido. Ambos os controladores são reiniciados ao entrar ou sair desse modo. Quando você sair desse modo, todos os serviços continuarão e devem ser íntegros. (Isso pode levar alguns minutos.)

Se você precisar aplicar atualizações de modo de manutenção, você receberá um alerta por meio do portal de clássico Azure que você tenha as atualizações que devem ser instaladas. Este alerta incluirá instruções para usar o Windows PowerShell para StorSimple para instalar as atualizações. Após atualizar seu dispositivo, use o mesmo procedimento para alterar o dispositivo para o modo normal. Para obter instruções passo a passo, consulte [etapa 4: modo de manutenção de saída](#step4).

> [AZURE.IMPORTANT] 
> 
> - Antes de entrar no modo de manutenção, verifique se os dois controladores de dispositivo eficaz, verificando o **Status de Hardware** na página **manutenção** no portal de clássico do Azure. Se o controlador não está íntegro, contate o Microsoft Support para as próximas etapas. Para obter mais informações, vá para o suporte da Microsoft de contato. 
> - Quando estiver no modo de manutenção, você precisa aplicar a atualização primeiro em um controlador e, em seguida, no outro controlador.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Etapa 1: Conectar o console serial<a name="step1">

Primeiro, use um aplicativo como o acabamento para acessar o console serial. O procedimento a seguir explica como usar acabamento para conectar o console serial.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Etapa 2: Insira o modo de manutenção<a name="step2">

Depois de se conectar ao console, determine se há atualizações instalar e entrar no modo de manutenção para instalá-las.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Etapa 3: Instalar as atualizações<a name="step3">

Em seguida, instale as atualizações.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Etapa 4: Modo de manutenção de sair<a name="step4">

Por fim, saia do modo de manutenção.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar hotfixes através do Windows PowerShell para StorSimple

Ao contrário de atualizações do Microsoft Azure StorSimple, hotfixes são instalados de uma pasta compartilhada. Como com atualizações, há dois tipos de hotfixes: 

- Hotfixes regulares 
- Hotfixes do modo de manutenção  

Os procedimentos a seguir explicam como usar o Windows PowerShell para StorSimple para instalar regulares e hotfixes de modo de manutenção.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece com as atualizações, se você executar uma redefinição de fábrica do dispositivo?

Se um dispositivo for redefinido configurações de fábrica, todas as atualizações são perdidas. Após o dispositivo de redefinição de fábrica é registrado e configurado, você precisará instalar manualmente atualizações através do Azure portal clássico e/ou o Windows PowerShell para StorSimple. Para obter mais informações sobre fábrica redefinição, consulte [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [usar o Windows PowerShell para StorSimple administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
