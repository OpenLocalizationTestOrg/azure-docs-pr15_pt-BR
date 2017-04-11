<properties
    pageTitle="Configurar um novo dispositivo com o Azure AD durante a instalação | Microsoft Azure"
    description="Um tópico que explica como os usuários podem configurar Azure AD ingressar durante sua primeira experiência de execução."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurar um novo dispositivo com o Azure AD durante a instalação

No Windows 10, os usuários podem participar seus dispositivos ao Azure Active Directory (AD Azure) na experiência de primeira execução (FRX). Isso permite que as organizações distribuir dispositivos têm embalagem para seus funcionários ou alunos ou escolherei seus próprios dispositivos (CYOD).
Se o Windows 10 Professional ou o Windows 10 Enterprise edições estiver instalado em um dispositivo, a experiência padrão é o processo de configuração para dispositivos de empresa.

## <a name="to-join-a-device-to-azure-ad"></a>Para ingressar em um dispositivo ao Azure AD


1. Quando você ativar seu novo dispositivo e iniciar o processo de instalação, você verá a mensagem **Getting Ready** . Siga os avisos para configurar seu dispositivo.
2. Comece Personalizando sua região e idioma. Aceite os termos de licença de Software da Microsoft.
![Personalizar para sua região](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Selecione a rede que você deseja usar para se conectar à Internet.
4. Selecione se você estiver usando um dispositivo pessoal ou um dispositivo de empresa. Se for empresa, clique neste **dispositivo pertence à minha organização**. Isso inicia a experiência de participar do Azure AD. A seguir é uma tela que você verá se estiver usando o Windows 10 Professional.
<center>
![Quem é responsável por essa tela do computador](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Digite as credenciais que foram fornecidas pela sua organização.
<center>
![Tela de entrada](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Depois que você inserir seu nome de usuário, um locatário correspondente está localizado no Azure AD. Se você estiver em um domínio federado, você será redirecionado ao seu servidor de seguro Token STS (serviço) no local — por exemplo, Federação serviços de Active Directory (AD FS).
7. Se você for um usuário em um domínio não federado, insira suas credenciais diretamente na página Azure AD hospedado. Se a marca da empresa foi configurada, você verá também logotipo da sua organização e dá suporte a texto.
8.  Você será solicitado para um desafio de autenticação multifator. Esse desafio é configurável por um administrador de TI.
9.  Azure AD verifica se esse usuário/dispositivo requer registro no gerenciamento de dispositivo móvel.
10. Windows registra o dispositivo no diretório da organização no Azure AD e registra-lo no gerenciamento de dispositivo móvel, se apropriado.
11. Se você for um usuário gerenciado, o Windows leva você para a área de trabalho durante o processo de entrada automática.
12. Se você for um usuário federado, você for orientado a tela de entrada do Windows para inserir suas credenciais.

> [AZURE.NOTE] Não há suporte para ingressar em um domínio do Active Directory do Windows Server local na experiência de prontos do Windows. Portanto, se você planeja ingressar um computador em um domínio, você deve selecionar o link **Configurar o Windows com uma conta local** , em vez disso. Você pode ingresse no domínio das configurações em seu computador enquanto você já fez antes.

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
