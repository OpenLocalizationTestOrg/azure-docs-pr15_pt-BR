<properties 
   pageTitle="Alterar a senha de administrador do dispositivo virtual StorSimple | Microsoft Azure"
   description="Descreve como usar o portal de clássico Azure ou web Array Virtual StorSimple interface do usuário para alterar a senha de administrador do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Alterar a senha de administrador do dispositivo de Array Virtual StorSimple

## <a name="overview"></a>Visão geral

Quando você usa a interface do Windows PowerShell para acessar o dispositivo virtual StorSimple, você precisa digitar uma senha de administrador do dispositivo. Quando o dispositivo de StorSimple primeiro está provisionado e iniciado, a senha padrão é *Senha1*. Para a segurança dos seus dados, a senha padrão expira a primeira vez que você entrar e se for necessário alterar essa senha.

Você também pode usar o Azure portal clássico ou a web local UI para alterar a senha de administrador do dispositivo a qualquer momento após o dispositivo está implantado em seu ambiente de produção. Cada um desses procedimentos é descrito neste artigo.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Usar o portal de clássico Azure para alterar a senha

Execute as seguintes etapas para alterar a senha de administrador do dispositivo por meio do portal de clássico Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Para alterar a senha de administrador do dispositivo através do portal de clássico Azure

1. No portal do, clique em **dispositivos** > **configuração** para seu dispositivo.

2. Role até a seção de **Senha de administrador do dispositivo** . Fornece uma senha de administrador que contenha de 8 a 15 caracteres. A senha deve ser uma combinação de caracteres em maiusculas, minúsculas, numérico e especial.

3. Confirme a senha.

4. Clique em **Salvar** na parte inferior da página.

A senha de administrador do dispositivo agora deve ser atualizada. Você pode usar essa senha modificada para acessar o dispositivo localmente.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Usar a web de Array Virtual StorSimple interface do usuário para alterar a senha

Execute as seguintes etapas para alterar a senha de administrador do dispositivo por meio da interface do usuário da web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a senha de administrador do dispositivo por meio da interface do usuário da web local

1. Na web local da interface do usuário, clique em **manutenção** > **Alterar senha** para seu dispositivo.

    ![Alterar senha 1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Insira a **senha atual**.

3. Fornece uma **nova senha**. A senha deve ter pelo menos 8 caracteres. Ele deve conter 3 de 4 dos procedimentos a seguir: caracteres em maiusculas, minúsculas, numérico e especial.

    Observe que a sua senha não pode ser o mesmo que as últimas 24 senhas.

3. Redigite a senha para confirmá-la.

    ![Alterar Senha2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Na parte inferior da página, clique em **Aplicar**. A nova senha será aplicada. Se a alteração de senha não for bem-sucedido, você verá o seguinte erro.

    ![Erro de senha](./media/storsimple-ova-change-device-admin-password/image42.png)

    Depois que a senha é atualizada com êxito, você será notificado. Em seguida, você pode usar essa senha modificada acessar o dispositivo localmente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
