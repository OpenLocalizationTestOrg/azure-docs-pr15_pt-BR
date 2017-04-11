<properties 
   pageTitle="Altere suas senhas StorSimple | Microsoft Azure" 
   description="Descreve como usar o serviço do Gerenciador de StorSimple para alterar sua senha de administrador do Gerenciador de instantâneo StorSimple e dispositivo." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Usar o serviço do Gerenciador de StorSimple para alterar suas senhas StorSimple

## <a name="overview"></a>Visão geral 

O portal de clássico Azure página **Configure** contém todos os parâmetros de dispositivo que você pode reconfigurar em um dispositivo de StorSimple que é gerenciado por um serviço Gerenciador de StorSimple. Este tutorial explica como você pode usar a página **Configurar** para alterar o administrador do dispositivo ou senha do Gerenciador de instantâneo StorSimple.

## <a name="change-the-device-administrator-password"></a>Alterar a senha de administrador do dispositivo

Quando você usa a interface do Windows PowerShell para acessar o dispositivo de StorSimple, você precisa digitar uma senha de administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registrado com um serviço, a senha padrão para esta interface é *Senha1*. Para a segurança dos seus dados, você precisa alterar essa senha no final do processo de registro. Você não pode sair do processo de registro sem alterar essa senha. Para obter mais informações, consulte [etapa 3: configurar e registrar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A senha que foi definida primeiro por meio da interface do Windows PowerShell durante o registro, em seguida, pode ser alterada através do portal de clássico Azure. Execute as seguintes etapas para alterar a senha de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a senha de administrador do dispositivo

1. No portal do clássico, clique em **dispositivos** > **Configurar** para o seu dispositivo.

2. Role até a seção de **Senha de administrador do dispositivo** . Fornece uma senha de administrador que contenha de 8 a 15 caracteres. A senha deve ser uma combinação de 3 ou mais dos caracteres em maiusculas, minúsculas, numérico e especial.

3. Confirme a senha.

4. Clique em **Salvar** na parte inferior da página.

A senha de administrador do dispositivo agora deve ser atualizada. Você pode usar essa senha modificada para acessar a interface do Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Alterar a senha de StorSimple Gerenciador de instantâneo

Gerenciador de instantâneo StorSimple software reside em seu host do Windows e permite aos administradores gerenciar backups do seu dispositivo de StorSimple na forma de locais e na nuvem instantâneos.

Ao configurar um dispositivo no Gerenciador de instantâneo StorSimple, você será solicitado a fornecer o endereço IP do dispositivo e a senha para autenticar seu dispositivo de armazenamento. Primeiro, essa senha está configurada por meio da interface do Windows PowerShell. Para obter mais informações, consulte [etapa 3: configurar e registrar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A senha que foi definida primeiro por meio da interface do Windows PowerShell durante o registro, em seguida, pode ser alterada através do portal clássico. Execute as seguintes etapas para alterar a senha do Gerenciador de instantâneo StorSimple.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para alterar a senha de StorSimple Gerenciador de instantâneo

1. No portal do clássico, clique em **dispositivos** > **Configurar** para o seu dispositivo.

2. Role até a seção **Gerenciador de instantâneo StorSimple** . Digite uma senha 14 ou 15 caracteres. Certifique-se de que a senha contém uma combinação de 3 ou mais dos caracteres em maiusculas, minúsculas, numérico e especial.

3. Confirme a senha.

4. Clique em **Salvar** na parte inferior da página.

A senha do Gerenciador de instantâneo StorSimple agora deve ser atualizada.
 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [segurança de StorSimple](storsimple-security.md).

- Saiba mais sobre como [modificar a configuração do seu dispositivo](storsimple-modify-device-config.md).

- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
