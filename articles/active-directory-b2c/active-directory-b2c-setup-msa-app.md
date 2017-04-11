<properties
    pageTitle="Azure B2C diretório ativo: Configuração de conta do Microsoft | Microsoft Azure"
    description="Fornece inscrição e entrar para consumidores com contas da Microsoft em seus aplicativos que são protegidos por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure B2C diretório ativo: Fornecer inscrição e entrar para consumidores com contas da Microsoft

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar a conta da Microsoft como um provedor de identidade no B2C Azure Active Directory (AD Azure), você precisa criar um aplicativo de conta da Microsoft e fornecer os parâmetros à direita. É necessário uma conta da Microsoft para fazer isso. Se você não tiver um, você pode obtê-lo em [https://www.live.com/](https://www.live.com/).

1. Acesse o [Portal de registro do aplicativo Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e entre com suas credenciais de conta da Microsoft.
2. Clique em **Adicionar um aplicativo**.

    ![Microsoft conta - adicionar um novo aplicativo](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Forneça um **nome** para o seu aplicativo e clique em **criar um aplicativo**.

    ![Conta da Microsoft - nome do aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copie o valor do **Id do aplicativo**. Você precisará dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário.

    ![Conta da Microsoft - Id de aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Clique em **Adicionar plataforma** e escolha **Web**.

    ![Microsoft conta - Adicionar plataforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Conta da Microsoft, da Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Redirecionar URIs** . Substitua **{locatário}** pelo nome do seu locatário (por exemplo, contosob2c.onmicrosoft.com).

    ![Conta da Microsoft - Redirecionar URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Clique em **Gerar nova senha** na seção **Segredos do aplicativo** . Copie a nova senha exibida na tela. Você precisará dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário. Esta senha é uma credencial de segurança importantes.

    ![Microsoft conta - gerar nova senha](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Conta da Microsoft - nova senha](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Marque a caixa que diz o **SDK do Live suporte** na seção **Opções avançadas** . Clique em **Salvar**.

    ![Conta da Microsoft, suporte SDK do Live](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurar a conta da Microsoft como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na lâmina B2C recursos, clique em **provedores de identidade**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Fornece um **nome** de amigável para a configuração do provedor de identidade. Por exemplo, insira "MSA".
5. Clique em **tipo de provedor de identidade**, selecione a **conta da Microsoft**e clique em **Okey**.
6. Clique em **configurar esse provedor de identidade** e insira o Id do aplicativo e a senha de aplicativo de conta da Microsoft que você criou anteriormente.
7. Clique em **Okey** e clique em **criar** para salvar a configuração de sua conta Microsoft.
