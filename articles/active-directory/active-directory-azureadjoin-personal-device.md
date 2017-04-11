

<properties
    pageTitle="Ingressar em um dispositivo pessoal à sua organização | Microsoft Azure"
    description="Explica como os usuários podem registrar seus dispositivos Windows 10 pessoais à sua rede corporativa e fornece etapas de implantação para um cenário BYOD."
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
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="join-a-personal-device-to-your-organization"></a>Ingressar em um dispositivo pessoal para sua organização

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Para ingressar em um dispositivo Windows 10 para sua organização

1.  No menu **Iniciar** , selecione **configurações**.
2.  Selecione **contas**e, em seguida, clique em **sua conta**.
3.  Clique em **Adicionar trabalho conta ou estudante**e digite na sua conta organizacional.
4.  Na página entrar para sua organização, digite seu nome de usuário e senha e clique em **Okey**.
5.  Você será solicitado para um desafio de autenticação multifator. (Esse desafio é configurável por um administrador de TI).
6.  Azure Active Directory (AD Azure) verifica se o dispositivo requer registro do gerenciamento de dispositivo móvel.
7.  Windows registra o dispositivo no diretório da organização no Azure AD e registra-lo no gerenciamento de dispositivo móvel, se apropriado.
8.  Se você for um usuário gerenciado, Windows guiará na área de trabalho durante o automático entrar.
9.  Se você for um usuário federado, você será levado a uma tela de entrada do Windows para inserir suas credenciais.

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
