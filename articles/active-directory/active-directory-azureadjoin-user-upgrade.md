<properties
    pageTitle="Configurar um dispositivo Windows 10 com o Azure AD de configurações | Microsoft Azure"
    description="Explica como os usuários podem participar ao Azure AD por meio do menu Configurações."
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

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurar um dispositivo Windows 10 com o Azure AD de configurações
Se você já estiver usando o Windows 7 ou Windows 8 e seu computador ou dispositivo foi atualizado para Windows 10, você pode participar ao Azure Active Directory (AD Azure) por meio do menu Configurações.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Para ingressar Azure AD no menu Configurações


1. No menu **Iniciar** , clique no botão **configurações** .
2. Em **configurações**, selecione **sistema**->**sobre**->**ingressar Azure AD**.
<center>
![Ingressar Azure AD no menu configurações](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Clique em **continuar** na janela de mensagem do Azure AD ingressar.
<center>
![Janela de mensagem de junção Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Fornece suas credenciais de entrada. Este processo de conexão incluirá todas as etapas que são necessárias para autenticação completa. Se você fizer parte de um locatário federado, seu administrador fornecerá a experiência de federação que está hospedada pela sua organização.
<center>
![Fornecer credenciais de entrada](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Se sua organização tiver configurado a autenticação multifator do Azure para juntar-se ao Azure AD, forneça o segundo fator antes de continuar.
6. Clique em **Aceitar** na tela de **Permitir que este dispositivo a ser gerenciado** .
7. Você verá a mensagem "o seu dispositivo agora está associado à sua organização no Azure AD".


## <a name="additional-information"></a>Informações adicionais
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
