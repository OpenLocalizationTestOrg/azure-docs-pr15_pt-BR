<properties
    pageTitle="Azure B2C diretório ativo: Configuração Google + | Microsoft Azure"
    description="Fornece inscrição e entrar para consumidores com contas do Google + em seus aplicativos que são protegidos por Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure B2C diretório ativo: Fornecer inscrição e entrar para consumidores com contas do Google +

## <a name="create-a-google-application"></a>Criar um aplicativo Google +

Para usar o Google + como um provedor de identidade no B2C Azure Active Directory (AD Azure), você precisa criar um aplicativo Google + e fornecer os parâmetros à direita. Você precisa de uma conta do Google + para fazer isso. Se você não tiver um, você pode obtê-lo em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Vá para o [Console de desenvolvedores do Google](https://console.developers.google.com/) e entre com suas credenciais de conta do Google +.
2. Clique em **Criar projeto**, insira um **nome de projeto**e, em seguida, clique em **criar**.

    ![Google + - Introdução](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - novo projeto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Clique em **Gerenciador de API** e clique em **credenciais** no painel esquerdo.
4. Clique na guia **OAuth consentimento tela** na parte superior.

    ![Google + - credenciais](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selecionar ou especificar um **endereço de Email**de válido, forneça um **nome de produto**e clique em **Salvar**.

    ![Google + - OAuth consentimento tela](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Clique em **novas credenciais** e escolha **OAuth ID do cliente**.

    ![Google + - OAuth consentimento tela](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Em **tipo de aplicativo**, selecione o **aplicativo Web**.

    ![Google + - OAuth consentimento tela](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Forneça um **nome** para seu aplicativo, insira `https://login.microsoftonline.com` no campo de **origens de JavaScript autorizados** , e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **autorizados redirecionar URIs** . Substitua **{locatário}** pelo nome do seu locatário (por exemplo, contosob2c.onmicrosoft.com). O valor de **{locatário}** diferencia maiusculas de minúsculas. Clique em **criar**.

    ![Google + - criar ID do cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie os valores de **ID do cliente** e **segredo de cliente**. Você precisará ambos configurar Google + como um provedor de identidade em seu locatário. **Segredo do cliente** é uma credencial de segurança importantes.

    ![Google + - segredo cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurar o Google + como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na lâmina B2C recursos, clique em **provedores de identidade**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Fornece um **nome** de amigável para a configuração do provedor de identidade. Por exemplo, insira "G +".
5. Clique em **tipo de provedor de identidade**, selecione **Google**e clique em **Okey**.
6. Clique em **configurar esse provedor de identidade** e insira a ID do cliente e o segredo de cliente do Google + aplicativo que você criou anteriormente.
7. Clique em **Okey** e, em seguida, clique em **criar** para salvar sua configuração Google +.
