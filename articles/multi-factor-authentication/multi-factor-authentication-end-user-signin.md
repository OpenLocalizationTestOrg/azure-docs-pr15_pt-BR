<properties
    pageTitle="Azure experiência MFA entrar com a autenticação multifator do Azure"
    description="Esta página fornece orientações sobre onde ir para ver os vários métodos de entrada no disponíveis com o Azure MFA."
    keywords="autenticação de usuário, o processo de conexão, entrar no telefone celular, entrar com o telefone comercial"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Experiência com a autenticação multifator Azure entrar
> [AZURE.NOTE]  A documentação a seguir fornecida nesta página mostra uma experiência típica entrar.  Para obter ajuda com entrar, consulte [está tendo problemas com a autenticação multifator do Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>Qual será sua experiência de entrada?
Dependendo de como entrar e usar a autenticação multifator, sua experiência serão diferentes.  Nesta seção, podemos fornecerá informações sobre o que esperar quando você entrar.  Escolha aquele que melhor descreve o que você está fazendo:


O que é que está a fazer?|Descrição
:------------- | :------------- |
[Entrando com o telefone celular ou o office](#signing-in-with-mobile-or-office-phone) | Este é o que você pode esperar de fazer logon usando seu telefone celular ou o office.
[Entrando com o aplicativo Microsoft Authenticator usando notificação](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Este é o que você pode esperar usando o aplicativo Microsoft Authenticator com notificações.
[Entrando com o aplicativo Microsoft Authenticator usando o código de verificação](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Este é o que você pode esperar usando o Microsoft Authenticator thapp com um código de verificação.
[Entrando com um método alternativo](#signing-in-with-an-alternate-method)|Isso mostrará o que esperar se você deseja usar um método alternativo.

## <a name="signing-in-with-mobile-or-office-phone"></a>Entrando com o telefone celular ou o office

As informações a seguir descrevem a experiência de usar autenticação multifator com seu telefone celular ou o office.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Para entrar com uma chamada para seu office ou telefone celular

- Entrar em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
- Microsoft chamará você.

![Chamadas da Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Atender o telefone e pressione a tecla #.

![Resposta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Você deve agora estar conectado.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Entrando com o aplicativo Microsoft Authenticator usando notificação

As informações a seguir descrevem a experiência de usar autenticação multifator com o aplicativo Microsoft Authenticator quando você receberá uma notificação.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Para entrar com uma notificação enviada o aplicativo Microsoft Authenticator

- Entrar em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
- Microsoft envia uma notificação.

![Microsoft envia uma notificação](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Atender o telefone e pressione a tecla de verificar.  Se sua empresa requer um PIN que você será solicitado para ele aqui.

![Verifique se](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Você deve agora estar conectado.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Entrando com o aplicativo Microsoft Authenticator usando o código de verificação

As informações a seguir descrevem a experiência de usar autenticação multifator com o aplicativo Microsoft Authenticator quando você estiver usando um código de verificação.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Entrar usando um código de verificação com o aplicativo Microsoft Authenticator

- Entrar em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
- Microsoft solicitará um código de verificação.

![Insira o código de verificação](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Abra o aplicativo Microsoft Authenticator em seu telefone e insira o código na caixa onde você está tentando entrar.

![Obtenha o código](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Você deve agora estar conectado.


## <a name="signing-in-with-an-alternate-method"></a>Entrando com um método alternativo


A seção a seguir mostrará como entrar com um método alternativo quando seu método principal pode não estar disponível.

### <a name="to-sign-in-with-an-alternate-method"></a>Entrar com um método alternativo

- Entrar em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
- Selecione usar uma opção de verificação diferente.  Você estará presente com diversas opções diferentes. O número que você vê será ser baseadas em quantos você tem a instalação.

![Use o método alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Escolha um método alternativo e entre.
