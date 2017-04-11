<properties
    pageTitle="Solução de problemas para problemas de acesso do Active Directory do Azure | Microsoft Azure"
    description="Aprenda etapas que você pode tomar para resolver problemas de acesso com recursos online da sua organização."
    services="active-directory"
    keywords="dispositivo de acesso baseado em condicional, registro de dispositivo, habilitar registro de dispositivo, o registro do dispositivo e o MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Solução de problemas para problemas de acesso do Active Directory do Azure

Tentar acessar intranet do SharePoint Online da sua organização e você receberá uma mensagem de erro de "acesso negado". O que você faz?

Este artigo aborda as etapas de solução que podem ajudá-lo a resolver problemas de acesso com recursos online da sua organização.

Para obter ajuda na resolução Azure Active Directory (AD Azure) acesso problemas, vá para a seção no artigo que abrange sua plataforma de dispositivo:

-   Dispositivo Windows
-   dispositivo iOS (Verifique regularmente para obter assistência com iPhones e iPads.)
-   Dispositivo Android (Verifique novamente em breve para obter assistência com Android telefones e tablets.)

## <a name="access-from-a-windows-device"></a>Acesso a partir de um dispositivo com Windows

Se o seu dispositivo executa um dos seguintes plataformas, procure nas próximas seções na mensagem de erro que é mostrado quando você tenta acessar um aplicativo ou serviço:

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Dispositivo não está registrado

Se seu dispositivo não estiver registrado no Azure AD e o aplicativo estiver protegido com uma política baseada em dispositivo, você poderá ver uma página que mostra uma destas mensagens de erro:

!["Você não pode ir daqui" mensagens para dispositivos não registrados] (./media/active-directory-conditional-access-device-remediation/01.png "Cenário")

Se seu dispositivo estiver domínio para o Active Directory em sua organização, tente o seguinte:

1.  Certifique-se de que você entrar no Windows usando sua conta do trabalho (sua conta do Active Directory).
2.  Conectar-se à sua rede corporativa por meio de uma rede virtual privada (VPN) ou DirectAccess.
3.  Depois que você estiver conectado, pressione a tecla de logotipo do Windows + tecla L para bloquear a sessão do Windows.
4.  Insira suas credenciais de conta de trabalho para desbloquear a sessão do Windows.
5.  Aguarde alguns minutos e tente novamente acessar o aplicativo ou serviço.
6.  Se você vir a mesma página, clique no link **mais detalhes** e contate o administrador com os detalhes.

Se seu dispositivo não estiver domínio e executa o Windows 10, você tem duas opções:

- Executar o Azure AD junção
- Adicionar sua conta corporativa ou de estudante para Windows

Para obter informações sobre como essas opções são diferentes, consulte [usando o Windows 10 dispositivos no seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar o Azure AD ingressar, siga estas etapas para a plataforma de seu dispositivo é executado no. (Junção do azure AD não está disponível em telefones do Windows).

**Atualização de aniversário 10 do Windows**

1.  Abra o aplicativo de **configurações** .
2.  Clique em **contas** > **acesso trabalho ou escola**.
3.  Clique em **Conectar**.
4.  Clique em **ingressar este dispositivo Azure AD**.
5.  Autenticar à sua organização, forneça autenticação multifator se solicitado e, em seguida, siga as etapas que são mostradas.
6.  Sair e, em seguida, entre com sua conta de trabalho.
7.  Tente acessar o aplicativo novamente.


**Atualização do Windows 10 de novembro de 2015**

1.  Abra o aplicativo de **configurações** .
2.  Clique em **sistema** > **sobre**.
3.  Clique em **ingressar Azure AD**.
4.  Autenticar à sua organização, forneça autenticação multifator se solicitado e, em seguida, siga as etapas que são mostradas.
5.  Sair e, em seguida, entre com sua conta do trabalho (sua conta do Azure AD).
6.  Tente acessar o aplicativo novamente.

Para adicionar sua conta corporativa ou escolar, siga estas etapas:

**Atualização de aniversário 10 do Windows**

1.  Abra o aplicativo de **configurações** .
2.  Clique em **contas** > **acesso trabalho ou escola**.
3.  Clique em **Conectar**.
4.  Autenticar à sua organização, forneça autenticação multifator se solicitado e, em seguida, siga as etapas que são mostradas.
5.  Tente acessar o aplicativo novamente.


**Atualização do Windows 10 de novembro de 2015**

1.  Abra o aplicativo de **configurações** .
2.  Clique em **contas** > **suas contas**.
3.  Clique em **Adicionar trabalho ou escola conta**.
4.  Autenticar à sua organização, forneça autenticação multifator se solicitado e, em seguida, siga as etapas que são mostradas.
5.  Tente acessar o aplicativo novamente.

Se seu dispositivo não estiver domínio e executa o Windows 8.1, para executar uma associação de local de trabalho e se inscrever no Microsoft Intune, siga estas etapas:

1.  Abra **configurações do PC**.
2.  Clique em **rede** > **local de trabalho**.
3.  Clique em **ingressar**.
4.  Autenticar à sua organização, forneça autenticação multifator se solicitado e, em seguida, siga as etapas que são mostradas.
5.  Clique em **Ativar**.
6.  Tente acessar o aplicativo novamente.


### <a name="browser-is-not-supported"></a>Não há suporte para navegador

Você pode ter acesso negado, se você está tentando acessar um aplicativo ou serviço, usando um dos seguintes navegadores:

- Chrome, Firefox ou qualquer outro navegador diferente Microsoft Edge ou Microsoft Internet Explorer no Windows 10 ou Windows Server 2016
- Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Você verá uma página de erro como esta:

![Mensagem "Você não pode ir daqui" para navegadores sem suporte] (./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A correção somente é usar um navegador compatível com o aplicativo para sua plataforma de dispositivo.

## <a name="next-steps"></a>Próximas etapas

[Acesso condicional do Azure Active Directory](active-directory-conditional-access.md)
