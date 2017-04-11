<properties
    pageTitle="Configuração de participar do Azure AD para seus usuários | Microsoft Azure"
    description="Explica como os administradores podem configurar participar do Azure AD para directory local e o registro do dispositivo."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>Configurar o Azure AD ingressar em sua organização

Antes de configurar o Azure Active Directory ingressar (Azure AD ingressar), você precisa sincronizar o seu diretório local de usuários na nuvem ou criar manualmente contas gerenciadas no Azure AD.

Instruções detalhadas para sincronizar seus usuários locais Azure AD é coberta de [integração de identidades seu local com o Active Directory do Azure](active-directory-aadconnect.md).


Para criar e gerenciar usuários no Azure AD manualmente, consulte [gerenciamento de usuários no Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurar o registro de dispositivo
1. Faça logon no portal do Azure como administrador.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia **diretório** , selecione seu diretório.
4. Selecione a guia **Configurar** .
5. Vá para a seção de **dispositivos** .
6. Na guia **dispositivos** , defina o seguinte:  
   * **Número de dispositivos por usuário máxima**: selecione o número máximo de dispositivos que um usuário pode ter no Azure AD.  Se um usuário atingir essa cota, eles não poderão adicionar dispositivos adicionais até que um ou mais dos seus dispositivos existentes são removidos.
   * **EXIGIR autenticação de MULTIFATOR para dispositivos de junção**: definir se os usuários são necessários para fornecer um segundo fator de autenticação para ingressar em seus dispositivos ao Azure AD. Para obter mais informações sobre a autenticação multifator Azure, consulte [Introdução ao Azure multi-Factor Authentication na nuvem](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Os usuários podem dispositivos de junção do AZURE AD**: selecione os usuários e grupos que têm permissão para ingressar dispositivos Azure AD.
   * **AZURE adicionais de Diante de administradores AD UNIDAS dispositivos**: com Azure AD Premium ou o pacote de mobilidade de Enterprise (EMS), você pode escolher quais usuários recebem direitos de administrador local no dispositivo. Os administradores globais e proprietários de dispositivo são concedidos direitos de administrador local por padrão.

<center>![Configurar o registro de dispositivo](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Após configurar participar do Azure AD para seus usuários, eles podem se conectar ao Azure AD por meio de seus dispositivos de pessoais ou corporativos.

Estes são os três cenários em que você pode usar para permitir que seus usuários configurar o Azure AD ingressar:

- Usuários ingressar em um dispositivo de empresa diretamente para o Azure AD.
- Um dispositivo de empresa no Active Directory local de associação de domínio de usuários e estender o dispositivo ao Azure AD.
- Usuários adicionam contas de trabalho ou da escola ao Windows em um dispositivo pessoal

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
