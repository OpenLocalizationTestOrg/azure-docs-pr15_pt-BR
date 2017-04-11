<properties
    pageTitle="Azure B2C diretório ativo: Configuração LinkedIn | Microsoft Azure"
    description="Fornecer inscrição e entrar para consumidores com contas do LinkedIn em seus aplicativos que são protegidos por Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure B2C diretório ativo: Fornecer inscrição e entrar para consumidores com contas do LinkedIn

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para usar o LinkedIn como um provedor de identidade no B2C Azure Active Directory (AD Azure), você precisa criar um aplicativo do LinkedIn e fornecer os parâmetros à direita. Você precisa de uma conta do LinkedIn para fazer isso. Se você não tiver um, você pode obtê-lo em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Acesse o [site de desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) e entre com suas credenciais de conta do LinkedIn.
2. Clique em **Meus aplicativos** na barra de menus superior e, em seguida, clique em **Criar aplicativos**.

    ![LinkedIn - novo aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. No formulário de **criar um novo aplicativo** , preencha as informações relevantes (**Nome da empresa**, **nome**, **Descrição**, **URL de logotipo do aplicativo**, **Uso do aplicativo**, **URL do site**, **Email comercial** e **Telefone comercial**).
4. Concordar com o **LinkedIn API termos de uso** e clique em **Enviar**.

    ![LinkedIn - aplicativo de registro](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copie os valores de **ID do cliente** e **Segredo de cliente**. (Você pode encontrá-las em **Chaves de autenticação**.) Você precisará ambos configurar LinkedIn como um provedor de identidade em seu locatário.

    >[AZURE.NOTE] **Segredo do cliente** é uma credencial de segurança importantes.

6. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Autorizados redirecionar URLs** (em **OAuth 2.0**). Substitua **{locatário}** pelo nome do seu locatário (por exemplo, contoso.onmicrosoft.com). Clique em **Adicionar**e, em seguida, clique em **Atualizar**. O valor de **{locatário}** diferencia maiusculas de minúsculas.

    ![LinkedIn - aplicativo de configuração](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurar o LinkedIn como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na lâmina B2C recursos, clique em **provedores de identidade**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Fornece um **nome** de amigável para a configuração do provedor de identidade. Por exemplo, insira "LI".
5. Clique em **tipo de provedor de identidade**, selecione **LinkedIn**e clique em **Okey**.
6. Clique em **configurar esse provedor de identidade** e insira a ID do cliente e o segredo de cliente do aplicativo LinkedIn que você criou anteriormente.
7. Clique em **Okey** e, em seguida, clique em **criar** para salvar sua configuração LinkedIn.
